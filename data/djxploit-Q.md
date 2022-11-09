## 1) Dependence on `block.timestamp` is risky

Block attributes can be manipulated by miners. Hence they should be avoided

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L52
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L51
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L85
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L28
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L50

## 2) Missing 0-address check

Addresses should be checked against 0 address, to avoid accidental damage to the protocol.

[https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L31-L34](https://www.notion.so/zghdkjas-a12b70f8c1064d4294076deb5abb31ed)
[https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L30-L31](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L30-L31)

## 3) Missing events for critical functions

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L118
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L57
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L94
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L65
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L140

## 4) Upgradeable contracts are missing storage gap variable

Storage gaps should be used in upgradeable contract to enable future development, and avoid storage collision

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L16
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Getters.sol#L13
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L12 
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L11
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L16

## 5) Lock a mapping instead of deleting it

Deleting a mapping , doesn't actually deletes it's value. Instead one should lock it by filling it with lock value, and then check whether the mapping has the value lock or not. If it has, then it is a deleted mapping.

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L169
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L212

## 6) `require` statements should have descriptive reasons to enhance readability of the code

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L297
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L221
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L43
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L45
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L238
https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L50

## 7) Resolve TODO comments before going into production

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L169
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L127 
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L28
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L94

## 8) Missing `indexed` keyword in events

`indexed` keywords helps in filtering an event faster by offchain monitoring tools

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L12
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L16
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L85
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L30-L35

## 9) Natspec standard should be followed

Every function usage, it's return variables and parameters should be explained, to enhance the readability of the code

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Getters.sol#L171-L172
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Getters.sol#L183-L184
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L35-L46
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L49-L55
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L87-L93
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L116-L123
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L143-L153
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L215-L219
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L134-L140
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L148-L154

## 10) TYPOS should be corrected

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IMailbox.sol#L95-L100
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L76
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L63
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L7
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L19