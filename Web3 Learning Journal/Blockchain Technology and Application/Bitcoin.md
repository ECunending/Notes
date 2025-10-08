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

