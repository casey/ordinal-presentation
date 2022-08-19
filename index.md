# Ordinals

# Today's Agenda
- Who am I?
- What are ordinals?

---

# Feel free to interrupt and ask questions!

---

# Who am I?
- I'm just this guy, you know?
- Into Bitcoin, Rust, generative art
- Last big project was Agora, a server for selling downloads for Lightning
  Network payments

---

# Why ordinals?
- Wanted a simple bitcoin-native protocol for assets
- Don't require any modification to the protocol
- Aesthetically appealing, so particularly well-suited for art

---

# What are ordinals?

---

# Ordinals are just serial numbers for satoshis

---

# They start at 0, and go up to 1,906,077,499,999,999 (so far!)

---

# Are transferred with a simple first-in-first-out algorithm

```
[2] [1] [3] → [4] [2]
```

```
[a b] [c] [d e f] → [? ? ? ?] [? ?]
```

```
[a b] [c] [d e f] → [a b c d] [e f]
```

---

# What about fees?

```
[2] [1] [3] → [4]
```

```
[a b] [c] [d e f] → [a b c d]
```

```
[SUBSIDY] [e f] → [SUBSIDY e f]
```

---

# Specification

```python
# subsidy of block at given height
def subsidy(height):
  return 50 * 100_000_000 >> height // 210_000

# first ordinal of subsidy of block at given height
def first_ordinal(height):
  start = 0
  for height in range(height):
    start += subsidy(height)
  return start

# assign ordinals in given block
def assign_ordinals(block):
  first = first_ordinal(block.height)
  last = first + subsidy(block.height)
  coinbase_ordinals = list(range(first, last))

  for transaction in block.transactions[1:]:
    ordinals = []
    for input in transaction.inputs:
      ordinals.extend(input.ordinals)

    for output in transaction.outputs:
      output.ordinals = ordinals[:output.value]
      del ordinals[:output.value]

    coinbase_ordinals.extend(ordinals)

  for output in block.transaction[0].outputs:
    output.ordinals = coinbase_ordinals[:output.value]
    del coinbase_ordinals[:output.value]
```

---

# What are ordinals good for?

---

# Rare and exotic sats

- [😐 Common](https://ordinals.com/ordinal/0°0′0″1‴)
- [🙂 Uncommon](https://ordinals.com/ordinal/0°1′1″0‴)
- [😏 Rare](https://ordinals.com/ordinal/0°2016′0″0‴)
- [🤤 Epic](https://ordinals.com/ordinal/0°0′336″0‴)
- [🥵 Legendary](https://ordinals.com/ordinal/1°0′0″0‴)
- [🤯 Mythic](https://ordinals.com/ordinal/0°0′0″0‴)
- [😎 Exotic](https://ordinals.com/ordinal/satoshi)
- [🤠 Bounties](https://ordinals.com/bounties/)

---

# Tokens and NFTs

If you want a token, you can just pick an ordinal to represent your token, and
use the location of the ordinal to represent ownership.

The person who controls the private key that corresponds to the public key of
the UTXO that contains the ordinal is the current owner of the token.

---

# What else are ordinals good for?

- Aesthetics!
- Supporting the fee market!
- Simplicity!

---

# The Index

- [mainnet](https://ordinals.com/)
- [signet](https://signet.ordinals.com/)

---

# What are ordinals not good for?

- Not having to make weird multi-step transactions to avoid hitting the dust
  limit
- Being efficient with block space
- Very high divisibility
- Small databases

---

# Ordinal NFTs: The Basic Idea

1. Hash: (ordinal || content hash)
2. Sign
3. Append pubkey, signature, and content
4. Et voilà: `736172790a20202020636f6e73656e74732c207065726d697373...`

---

# Ordinal NFTs

- No on-chain transaction to mint
- Store the NFT wherever
- Anyone who has access to the NFT will know the secret, hidden meaning of the
  ordinal

---

# Ordinal NFT Upgrades: Issued by ordinal owner

- Anyone can assign an off-chain NFT to an ordinal
- Kind of like digital graffiti
- In the future, may want to give some special status, like "self-issued" if the NFT is signed with a public key that held the ordinal

---

# Ordinal NFT Upgrades: Timestamping

- Age is of great interest to NFT collectors
- Including a timestamp in an NFT is an easy way to prove the age of an NFT

---

# Ordinal NFT Upgrades: Publicity

- Timestamps prove that an NFT was created before a particular time
- Timestamps *don't* prove that there wasn't anything that was created earlier
- It might be desirable to add a public issuance method, i.e. one where the chain can be scanned for such issuences, and a total ordering to be established over them
