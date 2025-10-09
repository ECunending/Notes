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
   
