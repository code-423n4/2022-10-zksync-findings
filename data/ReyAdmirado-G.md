
# GAS


| | issue |
| ----------- | ----------- |
| 1 | [Structs can be packed into fewer storage slots](#1-structs-can-be-packed-into-fewer-storage-slots) |
| 2 | [expressions for constant values such as a call to keccak256(), should use immutable rather than constant](#2-expressions-for-constant-values-such-as-a-call-to-keccak256-should-use-immutable-rather-than-constant) |
| 3 | [state var is defined but not used anywhere but ](#3-state-var-is-defined-but-not-used-anywhere-but) |
| 4 | [`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables](#4-x--y-costs-more-gas-than-x--x--y-for-state-variables) |
| 5 | [not using the named return variables when a function returns, wastes deployment gas](#5-not-using-the-named-return-variables-when-a-function-returns-wastes-deployment-gas) |
| 6 | [can make the variable outside the loop to save gas](#6-can-make-the-variable-outside-the-loop-to-save-gas) |
| 7 | [splitting require() statements that use `&&` saves gas](#7-splitting-require-statements-that-use--saves-gas) |
| 8 | [require() or revert() statements that check input arguments should be at the top of the function](#8-require-or-revert-statements-that-check-input-arguments-should-be-at-the-top-of-the-function) |
| 9 | [require() is repeated so it wastes has due to being checked again](#9-require-is-repeated-so-it-wastes-has-due-to-being-checked-again) |
| 10 | [use a more recent version of solidity](#10-use-a-more-recent-version-of-solidity) |
| 11 | [using `calldata` instead of `memory` for read-only arguments in external functions saves gas](#11-using-calldata-instead-of-memory-for-read-only-arguments-in-external-functions-saves-gas) |
| 12 | [internal functions only called once can be inlined to save gas](#12-internal-functions-only-called-once-can-be-inlined-to-save-gas) |
| 13 | [Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct](#13-multiple-addressid-mappings-can-be-combined-into-a-single-mapping-of-an-addressid-to-a-struct) |
| 14 | [abi.encode() is less efficient than abi.encodepacked()](#14-abiencode-is-less-efficient-than-abiencodepacked) |
| 15 | [usage of uint/int smaller than 32 bytes (256 bits) incurs overhead](#15-usage-of-uintint-smaller-than-32-bytes-256-bits-incurs-overhead) |
| 16 | [public functions not called by the contract should be declared external instead](#16-public-functions-not-called-by-the-contract-should-be-declared-external-instead) |
| 17 | [emit can be optimized](#17-emit-can-be-optimized) |


## 1. Structs can be packed into fewer storage slots

Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

`zkPorterIsAvailable` can be put after or before one of the address variables to make the struct smaller and save gas
- [Storage.sol#L104](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L104)


## 2. expressions for constant values such as a call to keccak256(), should use immutable rather than constant

- [L2ContractHelper.sol#L41](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/L2ContractHelper.sol#L41)


## 3. state var is defined but not used anywhere but 

even if they are for readability, consider making them comments instead

- [Config.sol#L12](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L12)
- [Config.sol#L23](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L23)
- [Config.sol#L26](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L26)


## 4. `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves gas

here `++s.diamondCutStorage.currentProposalId` would be even cheaper
- [DiamondCut.sol#L29](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L29)


## 5. not using the named return variables when a function returns, wastes deployment gas

- [Executor.sol#L26](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L26)


## 6. can make the variable outside the loop to save gas

`logSender`
- [Executor.sol#L112](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L112)

`priorityOp`
- [Executor.sol#L181](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L181)

`currentBlockCommitment`
- [Executor.sol#L247](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L247)

`facetAddr`
- [Getters.sol#L164](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Getters.sol#L164)

`facetToSelectors`
- [Getters.sol#L165](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Getters.sol#L165)

`hashedBytecode`
- [Mailbox.sol#L224](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L224)


## 7. splitting require() statements that use `&&` saves gas

this will have a large deployment gas cost but with enough runtime calls the split require version will be 3 gas cheaper

- [AllowList.sol#L96](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L96)


## 8. require() or revert() statements that check input arguments should be at the top of the function

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas*) in a function that may ultimately revert in the unhappy case.

bring this require before the 2 requires before it because of possible gas saves
- [Mailbox.sol#L66](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L66)


## 9. require() is repeated so it wastes has due to being checked again

first part of the require() is being repeated so under no circumstances it will happen
- [DiamondCut.sol#L56](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L56)


## 10. use a more recent version of solidity


Use a solidity version of at least 0.8.2 to get compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have `external` calls skip contract existence checks if the external call has a return value
Use a solidity version of at least 0.8.12 to get string.concat() to be used instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions


## 11. using `calldata` instead of `memory` for read-only arguments in external functions saves gas

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. 

`_lastCommittedBlockData`
- [Executor.sol#L152](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L152)

`_log`
- [Mailbox.sol#L43](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L43)


## 12. internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

`_collectOperationsFromPriorityQueue`
- [Executor.sol#L177](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L177)

`_executeOneBlock`
- [Executor.sol#L190](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L190)

`_getBlockProofPublicInput`
- [Executor.sol#L274](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L274)

`_verifyRecursivePartOfProof`
- [Executor.sol#L296](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L296)

`_maxU256`
- [Executor.sol#L349](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L349)

`_createBlockCommitment`
- [Executor.sol#L354](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L354)

`_blockPassThroughData`
- [Executor.sol#L362](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L362)

`_blockMetaParameters`
- [Executor.sol#L372](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L372)

`_blockAuxilaryOutput`
- [Executor.sol#L376](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L376)

`_L2MessageToLog`
- [Mailbox.sol#L75](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L75)

`_requestL2Transaction`
- [Mailbox.sol#L116](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L116)

`_writePriorityOp`
- [Mailbox.sol#L144](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L144)

`_hashFactoryDeps`
- [Mailbox.sol#L216](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L216)

`_depositFunds`
- [L1ERC20Bridge.sol#L136](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L136)

`_getDepositL2Calldata`
- [L1ERC20Bridge.sol#L149](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L149)
- [L1EthBridge.sol#L113](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L113)

`_getERC20Getters`
- [L1ERC20Bridge.sol#L164](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L164)

`_parseL2WithdrawalMessage`
- [L1ERC20Bridge.sol#L260](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L260)
- [L1EthBridge.sol#L214](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L214)


## 13. Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct

if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations. 

- [Storage.sol#L22-L23](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L22-L23)


## 14. abi.encode() is less efficient than abi.encodepacked()

- [Executor.sol#L381](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L381)

- [L1ERC20Bridge.sol#L81](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L81)
- [L1ERC20Bridge.sol#L168](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L168)

- [L1EthBridge.sol#L58](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L58)


## 15. usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

- [DiamondInit.sol#L30](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#L30)

- [Mailbox.sol#L125](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L125)
- [Mailbox.sol#L150](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L150)

- [L1ERC20Bridge.sol#L185](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L185)
- [L1ERC20Bridge.sol#L228](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L228)
- [L1ERC20Bridge.sol#L273](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L273)

- [L1EthBridge.sol#L138](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L138)
- [L1EthBridge.sol#L185](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L185)
- [L1EthBridge.sol#L223](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L223)


## 16. public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public and can save gas by doing so. 

`l2TokenAddress`
- [L1ERC20Bridge.sol#L282](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L282)
- [L1EthBridge.sol#L223](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L223)


## 17. emit can be optimized

use `newTotalBlocksCommitted` instead of `s.totalBlocksCommitted`
- [Executor.sol#L345](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L345)