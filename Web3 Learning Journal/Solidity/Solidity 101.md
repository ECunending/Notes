# Solidity 101
## 1. Hello Web3
Development tool -- Remix ([Website](remix.ethereum.org))
### The first solidity program 
``` Solidity
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

1. **Boolean**
```Solidity
bool public_bool = true;
```
Operations of boolean values include:
- ! NOT
- && AND
- || OR
- == equality
- != inequality
Note: The && and || follows a short-circuit evaluation rule.

2. **Integers**
``` Solidity
int public _int = -1
uint public _uint = 1 //unsigned integers
uint256 public _number = 20220330 // 256-bit unsigned integers
```
Operations of integer values include:
- Arithmetic Operator: +, -, *, %, /, **
- Inequality Operator (returns a boolean): <=, <, >, >=, ==, !=

3. **Addresses**
- Address: holds a 20 byte value (size of an Ethereum address)
- Payable Address: Same as address, but with additional numbers -- transfer and send to allow ETH transactions.
```solidity
address public _address = 0x7A58c0Be72BE218B41C608b7Fe7C5bB630736C71;
address payable public _address1 = payable(_address)
unit256 public balance = _address1.balance
```
4. **Fixed-size byte arrays**
Byte arrays have two types:
- Fixed length byte array: belongs to value type, including byte, bytes8, bytes32, etc. depending on the size of each element(maximum 32 bytes)
- Variable length byte array: belongs to reference type. The length of the array can be modified after declaration.
```solidity
\\fixed size byte arrays
byte32 public _byte32 = "MiniSolidity";
byte1 public _byte = _byte32[0];
```
5. **Enumeration**
It's a user-defined data type within Solidity, mainly used to assign names to uint.
```solidity
enum ActionSet{ Buy, Hold, Sell}
ActionSet action = ActionSet.Buy;

function enumToUint() external view returns(uint){
    return uint(action);
}
```

## 3. Function
1. **The form of function**
```solidity
function <function name>([parameter types[, ...]]){internal| external| public| private} [pure|view|payable]
[virtual| override] [<modifiers>][return (returns (<return types>)]{<function body>}
```
- function: key word
- function name
- parameter types
- {internal|external|public|private} Function visibility specifiers
  - internal: can only be accessed internally and by contracts deriving it
  - external: can only be called from other contracts or called by *this.f()* inside the contract, where *f* is the function name.
  - public: visible to all
  - private: can only be accessed within the contract, derived contracts cannot use it.
- pure|view|payable: Keywords that dictates a function's behavior

2. **What is Pure and View?**
Solidity adds these two keywords because of the high **gas fee**. If we don't rewrite these variables -- modify the state on chain(use pure and view), we don't need to pay gas fee. 

Behaviors that modify the state on chain: 
- Writing to state variables.
- Emitting events.
- Creating another contracts.
- Using selfdestruct.
- Sending Ether via calls.
- Calling any function not marked view or pure
- using low-level calls
- using inline assembly that contains certain episodes.

Pure: cannot read and write

View: can read but not write

Default: can read and write 

## 4. Return
1. **Return Values -- two keywords: Return and Returns**
- returns is added after the function name to declare variable type and variable name
- return is used in the function body and returns desired variables.
```solidity
function returnMultiple() public pure returns(uint256, bool, uint256[3] memory){
    return (1, true, [uint256(1), 2, 5]);
}
```
2. Also, we can indicate the name of the return variables in returns such that solidity automatically initialize those variables and return the value of those functions without adding the return keyword. 
```solidity
function returnNamed() public pure returns(uint256 _number, bool _bool, uint256[3] memory _array){
    _number = 2;
    _bool = false;
    _array = [uint256(3), 2, 1];
}
```
3. **Destructing Assignments**
```solidity
uint256 _number;
bool _bool;
uint256[3] memory _array;
(_number, _bool, _array) = returnNamed();
```

## 5. Data Storage 
1. **Reference type**
Reference types do not store value. Instead, they store the address/pointer of the data's location. Array, struct, and mapping takes up a lot of space, so we need to deal with the location of the data storage when using them. 

2. **Data Location**
- Storage: The state variable are storage by default, which are stored on-chain
- Memory: The parameters and temporary variables in the function generally use memory, which is stored in memory and not on-chain.
- calldata: similar to memory, stored in memory, not on-chain. The difference is that calldata variable can't be modified, and are generally used for function parameters.

Gas Consumption: $storage > memory > calldata$

When storage (a state variable of the contract) is assigned to the local storage (in a function), a reference will be created, and the changing value of the new variable will affect the original one. 
```solidity
uint[] x = [1,2,3];
function fStorage() public {
    uint[] Storage xStorage = x;
    xStorage[0] = 100;
}
```
Assigning storage to memory creates independent copies, and changes to one will not affect the other; and vice versa.

Assigning memory to memory will create a reference, and changing the new variable will affect the original variable.

Otherwise, assigning a variable to storage will create independent copies, and modifying one will not affect the other.
4. **Variable Scope**
There are three types of variables in Solidity according to their scope -- state variables, local variables, and global variables. 
- State variable: data is stored on-chain and can be accessed by in-contract functions, but their gas consumption is high; we can change the value of state variables in a function
- Local variable: they are only valid during function execution; they are invalid after function exit. The data of local variables are stored in memory, not on-chain, and their gas consumption is low.
- Global variable: they work in the global scope and are reserved keywords for solidity. They can be used directly in functions without declaring them.

Note: The unit of Ether: wei -- 1; gwei -- 1e9; ether -- 1e18


