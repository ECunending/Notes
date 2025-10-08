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
# 1. The form of function:
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

# 2. What is Pure and View?
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


   
