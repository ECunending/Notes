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
