# QA:

| | issue |
| ----------- | ----------- |
| 1 | [typo](#1-typo) |
| 2 | [use of floating pragma](#2-use-of-floating-pragma) |
| 3 | [require()/revert() statements should have descriptive reason strings or custom error](#3-requirerevert-statements-should-have-descriptive-reason-strings-or-custom-error) |
| 4 | [constants should be defined rather than using magic numbers](#4-constants-should-be-defined-rather-than-using-magic-numbers) |
| 5 | [inconsistency in comments](#5-inconsistency-in-comments) |
| 6 | [lines are too long](#6-lines-are-too-long) |
| 7 | [open todos](#7-open-todos) |
| 8 | [Outdated compiler version](#8-outdated-compiler-version) |
| 9 | [Use Underscores for Number Literals](#9-use-underscores-for-number-literals) |
| 10 | [event is missing indexed fields](#10-event-is-missing-indexed-fields) |
| 11 | [variable is never used in the contract so it may confuse the reader of code, consider commenting it or removing it to improve readability](#11-variable-is-never-used-in-the-contract-so-it-may-confuse-the-reader-of-code-consider-commenting-it-or-removing-it-to-improve-readability) |
| 12 | [require() should be used instead of assert()](#12-require-should-be-used-instead-of-assert) |
| 13 | [abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()](#13-abiencodepacked-should-not-be-used-with-dynamic-types-when-passing-the-result-to-a-hash-function-such-as-keccak256) |
| 14 | [use bool instead of making 2 constants to improve readability](#14-use-bool-instead-of-making-2-constants-to-improve-readability) |
| 15 | [2**<n> - 1 should be re-written as type(uint<n>).max](#15-2---1-should-be-re-written-as-typeuintmax) |


## 1. typo

Cotract --> Contract
- [DiamondProxy.sol#L7](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L7)

addreses --> addresses
- [Storage.sol#L36](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L36)

DiamondCutProposalCancelation --> DiamondCutProposalCancellation (event name)
- [DiamondCut.sol#L36](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L36)
- [IDiamondCut.sol#L22](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L22)

recurisiveAggregationInput --> recursiveAggregationInput (many uses in Executor.sol first is shown)

- [Executor.sol#L264](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L264)
- [IExecutor.sol#L58](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L58)

_blockAuxilaryOutput --> _blockAuxiliaryOutput
- [Executor.sol#L357](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L357)
- [Executor.sol#L376](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L376)

refference --> reference
- [L1ERC20Bridge.sol#L19](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L19)

exect
- [L1ERC20Bridge.sol#L76](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L76)

initiailize --> initialize
- [L1ERC20Bridge.sol#L76](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L76)



## 2. use of floating pragma
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

- [DiamondProxy.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol)
- [DiamondInit.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol)
- [Config.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol)
- [Storage.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol)
- [DiamondCut.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol)
- [Executor.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol)
- [Getters.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Getters.sol)
- [Base.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Base.sol)
- [Governance.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol)
- [Mailbox.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol)
- [L1ERC20Bridge.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol)
- [L1EthBridge.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol)
- [AllowList.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol)
- [AllowListed.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowListed.sol)
- [ReentrancyGuard.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/ReentrancyGuard.sol)


## 3. require()/revert() statements should have descriptive reason strings or custom error

- [Executor.sol#L43](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L43)
- [Executor.sol#L45](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L45)
- [Executor.sol#L297](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L297)

- [DiamondProxy.sol#L47](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L47)

- [L1EthBridge.sol#L221](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L221)
- [L1EthBridge.sol#L224](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L224)
- [L1EthBridge.sol#L238](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L238)


## 4. constants should be defined rather than using magic numbers 
Even assembly can benefit from using readable constants instead of hex/numeric literals

- [Executor.sol#L112](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L112)
- [Executor.sol#L116](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L116)
- [Executor.sol#L121](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L121)
- [Executor.sol#L130](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L130)
- [Executor.sol#L131](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L131)
- [Executor.sol#L135](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L135)
- [Executor.sol#L297](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L297)
- [Executor.sol#L300-L305](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L300-L305)


## 5. inconsistency in comments

- [Storage.sol#L35](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L35)


## 6. lines are too long
Usually lines in source code are limited to 80 characters. Its advised to keep lines lower than 120 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

- [DiamondCut.sol#L104](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L104)


## 7. open todos
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

- [Config.sol#L28](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L28)

- [Mailbox.sol#L94](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L94)
- [Mailbox.sol#L127](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L127)
- [Mailbox.sol#L169](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L169)


## 8. Outdated compiler version
Using very old versions of Solidity prevents benefits of bug fixes and newer security checks. Using the latest versions might make contracts susceptible to undiscovered compiler bugs


## 9. Use Underscores for Number Literals
There are multiple occasions where certain numbers have been hardcoded, either in variables or in the code itself. Large numbers can become hard to read.

- [Config.sol#L23](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L23)
- [Config.sol#L26](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L26)
- [Config.sol#L77](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L77)

- [L1ERC20Bridge.sol#L32](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L32)
- [L1ERC20Bridge.sol#L36](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L36)

- [L1EthBridge.sol#L26](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L26)
- [L1EthBridge.sol#L30](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#30)


## 10. event is missing indexed fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it’s not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

- [IMailbox.sol#L95](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IMailbox.sol#L95)

- [IExecutor.sol#L85](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L85)

- [IDiamondCut.sol#L30](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L30)


## 11. variable is never used in the contract so it may confuse the reader of code, consider commenting it or removing it to improve readability

- [Config.sol#L12](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L12)
- [Config.sol#L23](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L23)
- [Config.sol#L26](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L26)


## 12. require() should be used instead of assert()
Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as require()/revert() do. assert() should be avoided even past solidity version 0.8.0 as its documentation states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.

- [DiamondCut.sol#L16](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L16)


## 13. abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()
Use abi.encode instead 
If all the arguments are strings bytes.concat() should be used

- [Executor.sol#L283](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L283)

- [Mailbox.sol#L59](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L59)


## 14. use bool instead of making 2 constants to improve readability 

although it uses more gas it will lower readability

- [ReentrancyGuard.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/ReentrancyGuard.sol#L35-L36)


## 15. 2**<n> - 1 should be re-written as type(uint<n>).max
Earlier versions of solidity can use uint<n>(-1) instead. Expressions not including the - 1 can often be re-written to accommodate the change 

- [L2ContractHelper.sol#L53](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/L2ContractHelper.sol#L53)