# Low Risk

## [L-01] Floating pragma.

It's a good practice to avoid the use of floating pragma. Code must be compiled with the same version it as been tested the most. It also avoids the use of any nightly builds, which can have unexpected and unknown behaviors.

18 instances:

 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L3
 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#L3
 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L3
 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L3
 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Base.sol#L3
 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L3
 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L3
 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Getters.sol#L3
 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L3
 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L3
 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L3
 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L3
 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L3
 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L3
 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/ReentrancyGuard.sol#L3
 - https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L3
 - https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L3
 - https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L3

# Non Critical

## [N-01 ] Typo

12 instances:
 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L34

`current` -> `the current`
`proposal` -> `the proposal`

 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L7

`Cotract` -> `Contract`

 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L30

`contain` -> `contains`

 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L216

`then` -> `than`

 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L27

`pending` -> `a pending`

 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L19

`standard` -> `a standard`
`refference` -> `reference`

 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L76

`exect` -> `exact`
`initiailize` -> `initialize`

 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L16

`contracts` -> `contract`

 - https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L36

`addreses` -> `addresses`