# 1) Use calldata instead of memory
If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory.
6 Instances:
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Getters.sol#L69

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IGetters.sol#L30

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L74

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L114

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L120

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/ExternalDecoder.sol#L10

Recommended Mitigation Steps:
Consider to replace memory with calldata.