# Solidity 101
## 1. Hello Web3
Development tool -- Remix ([Website](remix.ethereum.org))
### The first solidity program 
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.21;
contract HelloWeb3{
    string public_string = "Hello Web3!";}
```
License -- Solidity version -- Main body

## 2. Value Types
1. Value Type: bool, integer etc. These variables directly pass value when assigned. 
2. Reference Type: arrays, structures. These variable take up more space, directly pass addresses (similar to pointers) when assigned, and can be modified with multiple variable names.
3. Mapping Type: the data structure that stores key-value pair -- similar to hash tables.
4. Function Types: It is in Value Type, actually, but it's very different from other types.

