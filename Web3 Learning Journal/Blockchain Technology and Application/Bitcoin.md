# Introduction
Since there's no English version of this class online, and this is a really great class for beginners to understand 
some fundamental concepts and technology in Blockchain, I saved my English notes here. The note not only includes the class content but also my summary and related supplementary knowledge. 

Also, my notes are divided in two parts -- Bitcoin & Ethereum (ETH). 

This is the first part -- Bitcoin.

# Lecture 1: Cryptography 
## 1. Cryptographic hash function 
- collision resistance (collision free) -- $x \neq y \implies H(x) \neq H(y)$
- hiding (premise: 1. the input space is large enough 2. It's well-distributed (even))

Those two features let blockchain serve as a digital commitment (digital sealed envelop)

Note: if the prerequisites are not met, we can add some nonce after the original number and calculate the hash value (H(x| nonce))
- puzzle friendly -- difficult to solve, but easy to verify
  
The second and third feature are quite similar, but they are not the same.

By the way, the hash function that Bitcoin uses is ***SHA-256***(Secure Hash Algorithm).

The process of mining is to find the hash value that is less than the target ($H(block header|nonce) < target$)
## 2. Public Key and Private Key
This is the asymmetric encryption algorithm: Each key pair consists of a public key and a corresponding private key. Key pairs are generated with cryptographic algorithms. 

**What's the process of money transaction on blockchain?**

If **A** wants to give some bitcoins to **B**, **A** uses the public key of **B** to encrypt the message, and **B** uses his private key to decrypt. The message should include the digital signature of **A** to prove the identity. To sign the digital signature, **A** uses his private key, and to check it, **B** uses the **A**'s public key. (Assume that there is a good source of randomness generating public and private keys.)

# Lecture 2: Data Structures of Bitcoin

## 1. Hash Pointer 
Hash pointer points to the previous block and contains its hash. Hash pointer does not work if there is a ring in the data structure.

If there is a ring, circular dependency will happen. 

## 2. Blockchain
Blockchain is a linked list using hash pointers.

The first block is called the **genesis block**, and the last block is called the **most recent block**.

They are linked with the hash pointer. (Note: it's the hash of all previous blocks, which is called **tamper-evident log**. This feature makes the blockchain immutable. If you change any of the block, the hash will become totally different. 

## 3. Merkel Tree
The difference between Merkel Tree and Binary Tree is that Merkel Tree uses the hash pointer. 

![IMG_0259](https://github.com/user-attachments/assets/b1e36356-200f-4cf1-b540-05374f1ec83a)

In Bitcoin, there are two types of nodes -- full node and light node (their responsibility will be discussed later in this class). 

Now, we only need to know that full node saves the block header and the block body while the light node (e.g. the Bitcoin Wallet on the iPhone) only saves the block header. 

We can do Merkel Proof with the Merkel Tree, and the path is highlighted above: When a light node wants to know more information about the transactions, they only need to know the "Red" Hash Value, and local compute the "Green" Hash Value.

Merkel Proof is a **Proof of Membership** (**Proof of Inclusion**). The complexity is $\Theta(log(n))$.

Supplement: **Proof of Non-Membership** -- Complexity: $\Theta(n)$

In fact, there is a more efficient way for proof of non-membership -- **Sorted Merkel Tree**. We list all the hash value of the transactions from small to big. After providing the merkel proof of the two transactions that is next to the given transaction, we check the correctness of both of them. If they are all correct (including the root hash), we have proven that those two transactions are next to each other. Therefore, the given transaction does not exist.  -- Complexity: $\Theta(log(n))$

In Bitcoin, we do not use the **Sorted Merkel Tree** because there is no need for non-membership proof. 

# Lecture 3: Protocol
## 1. Blockchain Details
Suppose A is transferring money to B.

Every transaction includes input (the source of money and A's public key) and output (B's public key and A's digital signature). In this way, we can avoid double spending. 

In fact, in every block, the transactions form a Merkel Tree.

![IMG_3FA960D137F7-1](https://github.com/user-attachments/assets/cddf798e-b078-417d-9d76-4f5ab33c9bcd)

Every block is composed of block header and block body.
- block header: includes version, hash of previous block header, Merkel root hash, target (of mining), nonce
- block body: transaction list.
There are full nodes (fully validating node) and light nodes.

## 2. Distributed Consensus
Account details need to gain distributed consensus to maintain the distributed hash table.

Some viewpoints:
- FLP Impossibility Result: In a asynchronous system, if there is a "faulty" person, the system cannot reach a consensus.
- CAP Theorem: A distributed data store cannot simultaneously guarantee Consistency, Availability, and Partition Tolerance (at most two of them).

## 3. Consensus in Bitcoin
If using vote, we need to verify the membership. For example, in the hyperledger fabric, only big company can be involved in. 

Otherwise, Sybil Attack will happen -- the malicious attackers will ceaselessly produce public and private key pairs to gain the vote right.

To avoid these scenario, Bitcoin uses the **Proof of Work**. There is a 4 byte nonce in the target of every block header. Finding the nonce can gain the vote right

When the hash of previous block header is not pointing to the longest valid chain, there are branches in the blockchain, which is also called forking attack. Usually, the next block should point to the longest valid chain. 

If two nodes gain the vote right simultaneously, other nodes just wait. The one that mine the next block first will gain the vote right, since that is the longest valid chain. Actually, this is a competition of hash rating and good luck. The other just becomes the orphan block. 

*Why do we “fight" for the right to add new blocks?*
Block reward. 

Coinbase Transaction is the only way to issue Bitcoin.

At first, the reward for each block is 50 BTC. After 21w blocks (approximately 4 years), the number becomes 25 BTC （halve).

Some Supplement:
- when the hash rate is larger, the probability of mining blocks increases.
- we use the word "mining"/"miner" because Bitcoin is like the digital gold. 

# Lecture 4: Implementation
## 1. BTC: transaction-based ledger 

Full nodes maintain UTXO (Unspent Transaction Output -- including the hash of generating the transaction and the number of transactions（1st/2nd/...), Avoid Double Spending.

Besides the block reward, miners can also get the transaction fee because people pay the fees to miners to avoid they only wrap their own transactions.

BTC: approximately a new block generates in 10 minutes.

$\frac{210000 * 10 min}{60min * 24h * 365 days} \approx 4 years$

Thus, after 2016 blocks, people change the difficulty of mining to ensure that on average, miners can find next new block every ten minutes.

Note: there's another mode -- account-based ledger, which is used in ETH.

## 2. More details about mining
To increase the space of looking for nonce, miners can also use the first few bits of coinbase transaction as extra nonce.

The reason is that the content in coinbase transaction does not matter, but changing it affects the Merkel Tree Root Hash.
![IMG_569DA1A6CA54-1](https://github.com/user-attachments/assets/24a24e90-071a-4b99-921f-4510dc1ed5de)

Everytime miners try nonce can be viewed as a Bernoulli Trail -- a random experiment with binary outcome. 

Bernoulli Process: A sequence of independent Bernoulli trail (memoryless, which means that the former results have no effect on the later results.)

When mining, the Bernoulli process is approximately the Poisson process(many times but low probability each time)
![IMG_BE33F8DD6333-1](https://github.com/user-attachments/assets/b1d88ff6-b992-48fe-9e51-00042ed0d5e1)
The time to the next block follows exponential distribution. 

Because of this feature, mining is *process free*, that is, imagine miners have mined for 10 mins, and get nothing. Then, next block may still be 10 mins later. Thus, solving the mining process time is meaningless. 

In this way, Bitcoin is secured by mining, assuming that the majority of nodes are not malicious. 

We can also calculate the total number of BTCs.

$210000*50 + 210000 * 25 + \cdots = 210000 * 50 * (1+\frac{1}{2}+\frac{1}{4}+\cdots) = 21,000,000$

By the formula of geometric sequence sum:
$a+ar^2+ar^3+\cdots = \frac{a}{1-r}$ when $r<1$.

## 3. How blockchain protects BTC?
Sometimes, malicious nodes may gain the right to write the next block. 
1. He cannot steal BTC.
Without the private key and digital signiture, he can't get the coin. Even though it is written on the chain, the chain will not be the longest valid chain.
2. May double spending (very difficult)
In theory, the malicious hacker could implement the forking attack. That is, he write the transaction and rollback simultaneously to produce two branches. The trading platform saw the transaction block and thought that they got the money. However, the hacker generates next block after the rollback chain to make it become the longest valid chain.
This method is very difficult since in blockchain, other nodes basically wait for 6 blocks(six confirmation -- about 1 hour). 
Therefore, the immutability of blockchain is guaranteed based on probability. 

There's another method, which is more commonly used -- zero confirmation, because honest nodes won't receive the rollback chain, and there is a time difference between trading and delivering goods. 
![IMG_3E8C5CD8A72B-1](https://github.com/user-attachments/assets/2dfe0226-dc16-4abd-af9f-5551202ae1d0)

3. Hackers intentionally exclude some valid transactions out of the block.
This doesn't matter since other blocks will include them.
4. Selfish mining
Normally, miners find a block and immediately publish it.
On the contrary, hackers may find many new blocks and publish them all together to implement the forking attack. The prerequisite of doing so is that the hacker has very huge hash rate. 
   
# Lecture 5: Bitcoin Network
- Application layer: Bitcoin Blockchain
- Network layer: P2P Overlay Network
In BTC, every nodes are equivalent. There's no supernode or master node.

If one wants to join the network, he needs to know the seed node.

The principle of designing the BTC Network: simple, robust, but not efficient. (Message propagation is via flooding. First send to zero-distance node randomly, then mark this message)

The limit storage of one block is 1M. Thus, it takes several seconds for the information to all the nodes.

Best effort: A transaction may not be received by all nodes due to network latency, and the order of receipt may also vary. Additionally, some nodes might relay incorrect transactions.

# Lecture 6: The Difficulty of Mining
$Mining: H(block header) <= target$

The encryption algorithm in BTC is *SHA256*; its output space is $2^256$, which is very large.

$difficulty = \frac{difficulty-one-target}{target}$

If we don't change the difficulty, the next block will be mined faster and faster.

If the time to next block is very fast, new blocks don't have enough time to disseminate in Bitcoin network. Then, forking attack will becomes very common because there are many branches, and hackers can easily make theirs to become the longest valid chain.



target = target * (actual time -- time spent mining the last 2016 blocks / expected time -- 2016*10)

This formula is equivalent to the below one.

next difficulty = previous difficulty * (2 weeks/time to mine last 2016 blocks)

With the formula, we know that people need to change the difficulty of mining every 14 days.

By the way, the malicious nodes cannot change the mining difficulty by themselves because other nodes won't guarantee this behavior.

# Lecture 7: Mining
Full node:
- always online
- maintain the blockchain information locally
- maintain the UTXO in storage for quickly checking the correctness of the transactions.
- Check the validity of transactions on BTC network
- Decides which transactions will be include in new blocks
- Check the validity of new blocks mined by other miners
- Mining

Light node (simplified payment verification clients -- SPV clients):
- not always online
- only store block header
- only store transactions that relate to themselves
- can only check the validity of above transactions
- can't check the correctness of other blocks in the BTC network
- can check the difficulty of mining
- can check the longest chain, but don't know which longest chain is legal

Equipment for mining:
**CPU** to **GPU** to **ASIC** (Application Specific Integrated Circuit); ASIC can only be used for solving mining puzzles. 

Some coins will set their mining puzzles as the same as other popular coins to attract more miners. This strategy is called **merge mining**.

To be fair, some coins design alternative mining puzzle, which is ASIC resistance.

Mining Pool: 

A pool manager (full node) is connected with many miners. Pool manager is responsible for all tasks of full nodes except the mining. Miners only need to solve the mining puzzle. 

Ways to distribute profit: When each miner get a share (almost valid block), he said it to pool manager for proof of work. Then, the profits is distributed based on how much work each miner does.

Miners can't publish new blocks because the task is assigned by the pool manager ( the address of the block is from the pool manager); if he changes the address, he can't get share because the Merkle Tree Root changes, and pool manager knows that.

If a mining pool controls >50% hashrate, they can employ forking attack and even boycott, which means that they will exclude all of someone's transaction. However, they cannot steal coins since they don't have others' private keys. 

# Lecture 8: BTC Script Language
BTC is a stack-based language.
```solidity
"result":{
    "txid": "921a...dd24",
    "hash": "921a...dd24",
    "version": 1,
    "size": 226,
    "locktime": 0,
    "vin": [...],
    "vout": [...],
    "blockhash": " 0x2c...5c0b",
    "confirmation": 23,
    "time": 1530846727,
    "blocktime": 1530846727
}
```
Generally, locktime equals 0. If it is not zero, the transaction need to wait for {locktime} to be written in the blocks.

The input structure of transactions:
```solidity
"vin":[{
    "txid":"c0cb...c57b",
    "vout": 0,
    "scriptSig": {
        "asm": "3045...0018",
        "hex": "4830...0018"
    },
}]
```
The first two lines are about where the coins from.

Later, "scriptSig" will be replaced by input script.(just for notation)

The output structure of transactions:
<img width="906" height="595" alt="Screenshot 2025-10-14 at 4 36 44 PM" src="https://github.com/user-attachments/assets/22fa9d63-93cc-43ab-a51a-5a8dc222cb6d" />
Later, "scriptPubKey" will be replace by output script.(just for notation)
<img width="964" height="637" alt="Screenshot 2025-10-14 at 4 40 04 PM" src="https://github.com/user-attachments/assets/462de4da-60ed-4b55-a71e-d7b50058bece" />
To check the correctness, we put the output of A to B and the input of B to C together in early stage of BTC. However, considering some security problems, now wwe first check the input script, and then we check the output script.

There are some forms of input and output scripts:
- P2PK (Pay to Public Key)

input script: PUSHDATA(Sig)

output script: PUSHDATA (PubKey), CHECKSIG

- P2PKH (Pay to Public Key Hash) -- commonly used
  
input script: PUSHDATA(Sig), PUSHDATA (PubKey)

output script: DUP, HASH160, PUSHDATA(PubKeyHash), EQUALVERITY, CHECKSIG

- P2SH (Pay to Script Hash)
  
input script: PUSHDATA(Sig), PUSHDATA (serialized redeemScript)

output script: HASH160, PUSHDATA (redeemScript), EQUAL

## Proof of Burn ##
- output script

RETURN [zero or more ops or text]

The form of output is called Provably Unspendable/Prunable Outputs
- return False, then the output can't be spent, and UTXO can be pruned.
- Application
AltCoin (Alternative Coin) requires clients to burn (destroy) some BTC to get some AltCoins.

Spending only a few amount of BTC to write the hash of some content after the return. Then, it will be written into the blockchain.
