1. No checks of address to external function :-
(i hope this is low level)
code snippet:-
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#L25

 s.governor = _governor;
        s.validators[_validator] = true;

recommendation:-

add checks of address 
require(_governer  != address(0)&& _validator != address(0), "custom error" )
change to internal visibilty.




2. use != instead of > in require to save gas 

code snippet;-
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L100
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol#L23

3. use unchecked for ++i in for loop to avoid overflows
code snippet
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L132
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L153
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L94
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L173

recommendation :-
add  unchecked { ++i }

4.Change constant variable to private to save gas :-
code snippet:-
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol  // full varaible in this contract.

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L10
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L14

5. storage pointer to a structure is cheaper than copying each value of the structure into memory, same for array and mapping

description:-
It may not be obvious, but every time you copy a storage struct/array/mapping to a memory variable, you are literally copying each member by reading it from storage, which is expensive. And when you use the storage keyword, you are just storing a pointer to the storage, which is much cheaper

code snippet:-
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L23
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L90
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L134
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L155
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L175

Refernce:-
https://code4rena.com/reports/2022-08-olympus#g-02--storage-pointer-to-a-structure-is-cheaper-than-copying-each-value-of-the-structure-into-memory-same-for-array-and-mapping-7-instances

6.Use Custom Errors instead of Revert Strings to save Gas:-

description:-
Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas
Additionally, custom errors can be used inside and outside of contracts (including interfaces and libraries).
Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("I");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

code snippet:-
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol#L23
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol#L24
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol#L25
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L126
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L156
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L214

recommendation:-
Make one error file try import from that 

reference:-
https://code4rena.com/reports/2022-08-foundation#g-13-use-custom-errors-instead-of-revert-strings-to-save-gas
https://blog.soliditylang.org/2021/04/21/custom-errors/


---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

 