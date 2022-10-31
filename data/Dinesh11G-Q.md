### Unspecific Compiler Version Pragma

#### Impact
Issue Information: Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

Example
🤦 Bad:

pragma solidity ^0.8.0;
🚀 Good:

pragma solidity 0.8.4;

#### Findings:
```
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/bridge/interfaces/IL1Bridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/bridge/interfaces/IL2Bridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/common/AllowList.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/common/AllowListed.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/common/ReentrancyGuard.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/common/interfaces/IAllowList.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/common/libraries/UncheckedMath.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/common/libraries/UnsafeBytes.sol::3 => pragma solidity ^0.8.0;

2022-10-zksync/ethereum/contracts/zksync/Config.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/DiamondInit.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/DiamondProxy.sol::3 => pragma solidity ^0.8.0;
::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/Storage.sol::3 => pragma solidity ^0.8.0;
::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/Base.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/Getters.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IDiamondCut.sol::3 => pragma solidity ^0.8;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IExecutor.sol::3 => pragma solidity ^0.8;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IGetters.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IGovernance.sol::3 => pragma solidity ^0.8;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IMailbox.sol::3 => pragma solidity ^0.8;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IZkSync.sol::3 => pragma solidity ^0.8;
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/libraries/Merkle.sol::3 => pragma solidity ^0.8.0;
::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/libraries/PriorityQueue.sol::3 => pragma solidity ^0.8.0;
::3 => pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/ExternalDecoder.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/L2ContractHelper.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/L2ERC20Bridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/L2ETHBridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/interfaces/IL1Bridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/interfaces/IL2Bridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/interfaces/IL2EthInitializable.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/interfaces/IL2StandardToken.sol::3 => pragma solidity ^0.8.0;
```
#### Tools used
Manual