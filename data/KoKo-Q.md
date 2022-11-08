# QA ISSUES FOR [2022-10-ZKSYNC](https://github.com/code-423n4/2022-10-zksync)

## [L-01] Not emiting events in some important functions

Description: Emmiting events is recommended each time when a state variable's value is being changed or just some critical event for the contract has occurred. It also helps off-chain monitoring of the contract's state.

./ethereum/contracts/bridge/L1EthBridge.sol

```
L56:   function initialize(bytes calldata _l2BridgeBytecode) external reentrancyGuardInitializer {
```

./ethereum/contracts/bridge/L1ERC20Bridge.sol

```
L71:   function initialize(
```

## [L-02] `TODO`s should not be present in code

Description: Code architecture, incentives and error handling questions should be resolved before deployment

./ethereum/contracts/zksync/interfaces/IExecutor.sol

```
L56:   /// TODO: The verifier integration is not finished yet, change the structure for compatibility later
```

## [N-01] Use more recent version of solidity

./ethereum/contracts/zksync/DiamondProxy.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/interfaces/IGetters.sol

```
L3:    pragma solidity ^0.8.0;
```

./zksync/contracts/bridge/L2ETHBridge.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/bridge/interfaces/IL1Bridge.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/common/AllowListed.sol

```
L3:    pragma solidity ^0.8.0;
```

./zksync/contracts/bridge/interfaces/IL2EthInitializable.sol

```
L3:    pragma solidity ^0.8.0;
```

./zksync/contracts/bridge/interfaces/IL1Bridge.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/common/ReentrancyGuard.sol

```
L3:    pragma solidity ^0.8.0;
```

./zksync/contracts/bridge/interfaces/IL2Bridge.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/DiamondInit.sol

```
L3:    pragma solidity ^0.8.0;
```

./zksync/contracts/bridge/L2ERC20Bridge.sol

```
L3:    pragma solidity ^0.8.0;
```

./zksync/contracts/L2ContractHelper.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/interfaces/IGovernance.sol

```
L3:    pragma solidity ^0.8;
```

./ethereum/contracts/zksync/Storage.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/facets/Executor.sol

```
L3:    pragma solidity ^0.8.0;
```

./zksync/contracts/bridge/interfaces/IL2StandardToken.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/common/AllowList.sol

```
L3:    pragma solidity ^0.8.0;
```

./zksync/contracts/ExternalDecoder.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/facets/DiamondCut.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/facets/Mailbox.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/facets/Base.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/bridge/L1EthBridge.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/interfaces/IDiamondCut.sol

```
L3:    pragma solidity ^0.8;
```

./ethereum/contracts/common/interfaces/IAllowList.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/common/libraries/UnsafeBytes.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/facets/Getters.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/interfaces/IZkSync.sol

```
L3:    pragma solidity ^0.8;
```

./ethereum/contracts/zksync/libraries/Merkle.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/facets/Governance.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/bridge/interfaces/IL2Bridge.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/bridge/L1ERC20Bridge.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/Config.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/interfaces/IExecutor.sol

```
L3:    pragma solidity ^0.8;
```

./ethereum/contracts/zksync/libraries/Diamond.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/common/libraries/UncheckedMath.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/libraries/PriorityQueue.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/interfaces/IMailbox.sol

```
L3:    pragma solidity ^0.8;
```

./ethereum/contracts/common/L2ContractHelper.sol

```
L3:    pragma solidity ^0.8.0;
```

./zksync/contracts/bridge/L2StandardERC20.sol

```
L3:    pragma solidity ^0.8.0;
```

## [N-02] Not using the NatSpec format

./ethereum/contracts/common/libraries/UncheckedMath.sol

```
L1:    // SPDX-License-Identifier: MIT OR Apache-2.0
```

./zksync/contracts/bridge/interfaces/IL2EthInitializable.sol

```
L1:    // SPDX-License-Identifier: MIT OR Apache-2.0
```

./ethereum/contracts/zksync/interfaces/IDiamondCut.sol

```
L1:    // SPDX-License-Identifier: MIT OR Apache-2.0
```

./zksync/contracts/bridge/interfaces/IL2StandardToken.sol

```
L1:    // SPDX-License-Identifier: MIT OR Apache-2.0
```

./zksync/contracts/L2ContractHelper.sol

```
L1:    // SPDX-License-Identifier: MIT OR Apache-2.0
```

./ethereum/contracts/zksync/interfaces/IZkSync.sol

```
L1:    // SPDX-License-Identifier: MIT OR Apache-2.0
```

## [N-03] Non-library/interface files should use fixed compiler versions, not floating ones

./ethereum/contracts/zksync/facets/Mailbox.sol

```
L3:    pragma solidity ^0.8.0;
```

./zksync/contracts/bridge/L2ERC20Bridge.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/Storage.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/facets/Executor.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/bridge/L1EthBridge.sol

```
L3:    pragma solidity ^0.8.0;
```

./zksync/contracts/bridge/L2StandardERC20.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/facets/Base.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/facets/Governance.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/Config.sol

```
L3:    pragma solidity ^0.8.0;
```

./zksync/contracts/bridge/L2ETHBridge.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/DiamondInit.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/facets/DiamondCut.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/bridge/L1ERC20Bridge.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/common/ReentrancyGuard.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/common/AllowList.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/DiamondProxy.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/zksync/facets/Getters.sol

```
L3:    pragma solidity ^0.8.0;
```

./ethereum/contracts/common/AllowListed.sol

```
L3:    pragma solidity ^0.8.0;
```
