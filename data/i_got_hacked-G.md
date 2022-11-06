
##  Multiple mappings can be combined into a single mapping of `key` to a struct.

* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L87-L89
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L26-L30
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L22-L23


## `require()` statements that use `&&` should be split

* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L96-L99

## Use `calldata` instead of `memory` for external functions where the function argument is read-only

* https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/ExternalDecoder.sol#L10
* https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/ExternalDecoder.sol#L15
* https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/L2ContractHelper.sol#L6
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L6

## `internal` functions only called once can be `inlined` to save gas

* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L349

## Adding a return statement when the function defines a `named` return variable, is redundant

* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L67-L78