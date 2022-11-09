**********************************************************G-01 Always use pre-computed constant values**********************************************************

- [https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Config.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Config.sol#L3)

Defining constants as an expression like this `uint256 constant *INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES* **=** *4* **+** *64* ***** *4896*;` results in the expression being executed every time the constant is read, instead of caching the value. Always pre-compute the value and just add an explaining comment. 

************G-02 Replace all `require` statements with custom errors**

- [https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L205](https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L205)

Solidity now supports custom errors that cost much less gas than `require` statements with revert strings. Replace all `require` statements with custom errors

************************************G-03 Pack struct variables in 256 byte slots always************************************

- [https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IExecutor.sol#L15](https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IExecutor.sol#L15)

Every time you define a `struct` it is best to define it’s fields in a sequence so that they fill 256 byte slots - this will save gas on reads from storage.

************************************************************************G-04 Always compare uint with `!= 0` instead of `> 0`**

- [https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L210](https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L210)

Comparing uint values with `!= 0` is cheaper than `> 0`

********************************************************************************************************************G-05 Indexed params are cheaper on events, always use them********************************************************************************************************************

- [https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/interfaces/IAllowList.sol#L11](https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/interfaces/IAllowList.sol#L11)

Make sure all events use the maximum of 3 `indexed` parameters to save gas

********G-06 `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`and `while`loops**

- [https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L153](https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L153)

All for loops in `Diamond.sol` use `++i` and it can be wrapped in an unchecked block to save gas

****************************G-07 Use `++var` instead of `var += 1`**

- [https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L29](https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L29)

The code is `s.diamondCutStorage.currentProposalId **+=** *1*;` change it to `++s.diamondCutStorage.currentProposalId;` to save gas.

******G-08 Using a `storage` pointer instead of `memory` for structs when doing only read-only operations is cheaper**

- [https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/DiamondProxy.sol#L26](https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/DiamondProxy.sol#L26)

The structure reference in the fallback function in `DiamondProxy` can be of type `storage` to save gas.