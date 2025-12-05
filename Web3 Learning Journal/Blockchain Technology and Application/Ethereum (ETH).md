# Description
This is the second part -- Ethereum (ETH)

# Lecture 13: Ethereum Overview
Ethereum average block time -- 12 seconds

Mining puzzle -- memory hard (ASIC resistance)

Proof of Stake instead of Proof of Work

Support Smart Contract (If we can decentralize currency, what else can we decentralize?)

By the way, the minimum unit of BTC is Satoshi, and the minimum unit of ETH is Wei.

# Lecture 14: Ethereum Account
ETH utilizes account-based ledger instead of transaction based ledger in BTC. Thus, users can see how many ETH they have. To check the validity. we only need to compare saving with spending of that account, and we don't need to point out where the ETH is from.

In this way, double spending attack is impossible. 

Disadvantages: Replay Attack -- the payee broadcasts a transaction several times. (Note: it's impossible in BTC)

Solution: let nonce (counter/sequence number) as the transaction number and write nonce in each transaction

There are two kinds of accounts in ETH:
- Externally Owned Account (balance & nonce)
- Smart Contract Account (can't make a transaction & code and storage)

# Lecture 15: ETH Data Structure
ETH account address is 160 bit -- 20 bytes. ETH needs to maintain a mapping from address to state.

1. If we use the hash table (because it seems like a key-value pair), when there is a new transaction, the hash table changes, and we need to construct a new merkle tree to provide a new merkle proof, which cost a lot of time. 

BTC does not such problem since the merkle tree in BTC is immutable. 

2. If we use merkle tree directly and change a small part of the tree each time, what will happen?
- if we don't use sorted merkle tree, we can't prove non-membership AND the merkle tree users construct is not unique (the order of leaf nodes is different), which leads to the difference of root hash value.
- if we use sorted merkle tree, when there's a new account, we need to construct a new merkel tree (results similar to the hash table method)

3. Trie (Data Structure)
Trie is a is a specialized search tree data structure used to store and retrieve strings from a dictionary or set. Unlike a binary search tree, nodes in a trie do not store their associated key. Instead, each node's position within the trie determines its associated key, with the connections between nodes defined by individual characters rather than the entire key.

We can solve all problems mentioned above using Trie. 

However, there's a waste of storage in Trie. After path compression, we can get a Patricia tree (Patricia trie).


