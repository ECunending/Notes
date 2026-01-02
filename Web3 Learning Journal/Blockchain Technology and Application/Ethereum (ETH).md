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

1. **If we use the hash table (because it seems like a key-value pair), when there is a new transaction, the hash table changes, and we need to construct a new merkle tree to provide a new merkle proof, which cost a lot of time.**

BTC does not such problem since the merkle tree in BTC is immutable. 

2. **If we use merkle tree directly and change a small part of the tree each time, what will happen?**
   
- if we don't use sorted merkle tree, we can't prove non-membership AND the merkle tree users construct is not unique (the order of leaf nodes is different), which leads to the difference of root hash value.
- if we use sorted merkle tree, when there's a new account, we need to construct a new merkel tree (results similar to the hash table method)

3. **Trie (Data Structure)**
   
Trie is a is a specialized search tree data structure used to store and retrieve strings from a dictionary or set. Unlike a binary search tree, nodes in a trie do not store their associated key. Instead, each node's position within the trie determines its associated key, with the connections between nodes defined by individual characters rather than the entire key.

We can solve all problems mentioned above using Trie. 

However, there's a waste of storage in Trie. After path compression, we can get a Patricia tree (Patricia trie), which is more effective.

4. **MPT**
   
What ETH uses is Modified MPT (Merkle Patricia Tree).

A Merkle-Patricia trie is deterministic and cryptographically verifiable: The only way to generate a state root is by computing it from each individual piece of the state, and two states that are identical can be easily proven so by comparing the root hash and the hashes that led to it (a Merkle proof). 

ETH data structure is a "big" MPT with lots of "small" MPT (each block corresponding to a MPT)

Each Ethereum block generates a new MPT, but most of its nodes are the same as those in the previous MPT — only new branches need to be added. The reason it isn’t modified in place is that temporary forks are common, and the losing fork must be rolled back. At the same time, the way smart contracts are implemented makes it impossible to reconstruct previous states solely from transactions, so historical information needs to be preserved.

There are three kinds of "trees" in ETH: State Tree (store key-value pair), Transaction Tree, and Receipt Tree. 

5. **How do the state of the account store in the state tree?**

Use RLP (Recursive Length Prefix).

# Lecture 16: ETH Transaction Tree and Receipt Tree

They are all MPT. Every transaction has a receipt. With them, we can execute merkle proof more easily.

Bloom Filter (data structure): whether the specific element is in the large list or not. With bloom filter, we have higher efficiency, but at the same time, we may encounter false positive situations (impossible for false negative). 

We can't remove elements in the (binary) bloom filter. By only checking the bloom filter on the transaction header, we don't need to consider many unrelated blocks. 

We can view ETH as a transaction-driven state machine. As an analogy, we can also view BTC as a transaction-driven state machine where UTXO is the state. 

# Lecture 17: GHOST

The **GHOST (Greedy Heaviest Observed Subtree) Protocol** is a consensus mechanism designed to improve efficiency and security of blockchain networks like ETH. 

If ETH uses what BTC uses, there will be many branches and orphan block (stale block), which is not beneficial to individual miner, leading to mining centralization (centralization bias). Thus, ETH uses **GHOST Protocol**.

The 1st version of GHOST: In ETH, orphan blocks are called **uncle block**. If the new block includes an uncle block, the miner can gain $\frac{1}{32}$ extra block reward, and the uncle block can gain $\frac{7}{8}$ of the block reward. Also, a new block can gain at most two uncle blocks. In this way, the branches will soon merge together. 

Some probelms: 

- more than two uncle blocks
- the uncle block appears after the new block
- because of some business competitions, the new block deliberately does not include the uncle block

Solution: all previous orphan blocks are uncle blocks of the blocks in the longest valid chain. 

To avoid too many uncle blocks, in ETH, the contemporary uncle block can gain $\frac{7}{8}$ block reward. The uncle block at a distance of 2 can gain $\frac{6}{8}$ block reward, and so on (at most seven generations -- six distance -- $\frac{2}{8}$)

In ETH, there are static reward (block reward) and dynamic reward (gas fee). Uncle blocks can only get the "bonus" block reward; they can't get gas fee (very small amount). 

When a new block includes the uncle block, it will not execute the transactions in the uncle block (there will be conflicts sometimes) and check the transaction validity. It will only check whether the uncle block matches the mining difficulty by checking the block header. 

Only the first uncle block can get the reward, and the blocks after the "uncle block chain" will gain nothing because if don't do so, the cost of forking attack is very low, and they can get the reward anyways. 

# Lecture 18: Mining Algorithm in ETH

