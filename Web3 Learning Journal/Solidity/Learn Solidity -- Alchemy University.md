# Learn Solidity
## Part One: Solidity Introduction
## 1. Smart Contrasts
### Smart Contracts Overview
A smart contract is blockchain-deployed code.

If we want to deploy a contract, we need to compile to bytecode, and send a transaction containing the bytecode to an EVM node. Then, the code calculates an address for your new contract. 

[Opocode for the EVM](https://ethereum.org/en/developers/docs/evm/opcodes/)

Each opocode spends certain amount of gas. Gas is its own unit, which is seperate from what people used to pay for gas -- the chain's native currency(e.g. for ETH, it's ether).

Transaction lifetime cycle: it starts at EOA (externally owned account -- public, private key). EOA signs the transaction with the private key. Transaction set a gas limit. Transaction occur sequentially. Transaction sends calldata, targetting a contract method. Also, smart contracts can call each other within one transaction. 
### Value Types
Basically, we can view a Contract as a Class. 
- unsigned integer(uint): a number without a sign
  - we can specify the bytes we need(e.g uint256, uint8)
  - uint is an alias for uint256
- integer: a number that could be positive or negative
- Boolean: either True or False (the default value of boolean is False)
- Enum: defining options for a value by name

Constructor: in contract, it means that the function can only run once upon deployment. 
