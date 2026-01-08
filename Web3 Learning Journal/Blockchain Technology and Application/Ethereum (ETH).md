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

**Blockchain is secured by mining.**

To make mining available to more people, people aim to design ASIC resistance mining puzzle, such as memory hard mining puzzle. 

Ex. LiteCoin used to be the second popular cryptocurrency. It uses scrypt as mining puzzle, which is a time-memory tradeoff. The disadvantage is that light node also faces memory-hard problem. Thus, solving and verifying are both difficult. In LiteCoin, the memory is 128K, which is not ASIC-resistant. 

**Principle of designing mining puzzle: difficult to solve, but easy to verify.**

Data set in ETH -- the smaller one (16M cache) & the bigger one (1G, DAG). They increase with time. Thus, the light node only need to save the smaller data set. 

The mining algorithm（ethash): There is an array where the hash of the seed is placed in the first element, then the hash of the first element is placed in the second, and so on until the entire cache array is filled. Then an initial mix is generated using the element at index i % cache_size in the cache. Next, a loop runs 256 times: in each iteration, get_int_from_item is used to obtain the index of the next cache element to access, that cache element is combined with the mix to produce a new mix, and finally the hash of the mix is stored in the i-th position of the dataset.

ETH uses pre-mining (reserve lots of ETH for founders). Another related concept is pre-sale (it refers to participating in the early fundraising stage of a new crypto project by using Ether (ETH) as payment)

Now, ETH uses POS(Proof of Stake) instead of POW (Proof of Work), which means that the classical ETH mining stops.

# Lecture 19: Ethereum Mining Difficulty Adjustment Algorithm 

This part is outdated after ETH turns to POS. Thus, I omitted this part of the notes. 

# Lecture 20: Proof of Stake (PoS)
In PoW, people buy machines to mine; we can turn to **virtual mining**.

PoS: Validators are chosen to create new blocks based on the amount of cryptocurrency they "stake" (lock up) as collateral, eliminating the need for intense computation and drastically cutting energy use.

Advantages: Save energy; More Secure -- in PoW, coins with relatively small amount of users are vulnerable to real world attack (usually with lots of money); but in PoS, if they buy lots of the specific kind of coin, the price will skyrocket, making it hard to attack. 

In the early stage of PoS, one significant challenge is **nothing at stake** -- when there is a fork, people won't mine two chains simultaneously in PoW, but in PoS, they can stake two chains at the same time. 

In ETH, they use **Casper the Friendly Finality Gadget (FFG)**.

FFG: It introduces validators, who lock up a certain amount of ETH as a security deposit. Their role is to vote on which chain is the correct and longest valid one. Ethereum divides time into epochs (about 50 blocks). At the end of each epoch, validators vote on a checkpoint. Finality is reached through a two-step voting process, conceptually called prepare and commit. Each step requires approval from at least two-thirds of the total staked ETH. In practice, the protocol simplifies this by requiring two consecutive epochs to each receive a two-thirds majority vote for finalization. Validators who vote correctly earn rewards. Misbehavior, such as voting for conflicting chains, leads to slashing, where part or all of the validator’s stake is destroyed. Validators have lock-up and waiting periods, and after the waiting period ends without reports of misconduct, they can withdraw their stake and rewards.

![IMG_0368](https://github.com/user-attachments/assets/52ed4423-909b-4247-892b-d5bd9db7c6c6)

# Lecture 21: Smart Contract

Smart contract is some code in essence. The smart contract account saves the current execution state in the contract, including balance, nonce, code, and storage (which is a MPT). 

About the code, I will explain them more explicitly in **Solidity** part. 

# Lecture 22: The DAO

DAO - Decentralized Autonomous Organization. It's also a form of investor-directed venture capital fund. After launching in April 2016 via a token sale, it became one of the largest crowdfunding campaigns in history, but it ceased activity after much of its funds - in the form of US dollar exchanged for "Ether-crypto coins" - were taken in a hack in June 2016.

How to get your(investor) money back -- splitDAO, which is also a method to create a childDAO. The investor need to create his ow childDAO, and invest all of its money to himself to get the money back. Also, there is a 28-day holding period. 

However, hackers used Replay Attack and stole lots of ETH.
