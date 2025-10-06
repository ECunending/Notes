# Introduction
Since there's no English version of this class online, and this is a really great class for beginners to understand 
some fundamental concepts and technology, I saved my English notes. 

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

