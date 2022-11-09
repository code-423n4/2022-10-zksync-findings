# zkSync v2 Contest QA Report

## Summary

The following QA issues were found during the code audit:

1. Unspecific Compiler Version Pragma (54 instances)
2. `block.number` may exceed `type(uint32).max` (1 instance)

Total 55 instances of 2 issues.

## 1. Unspecific Compiler Version Pragma (54 instances)

Avoid floating pragmas for non-library contracts. While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain. It is recommended to pin to a concrete compiler version.

```solidity
ethereum/contracts/bridge/L1ERC20Bridge.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/bridge/L1EthBridge.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/bridge/interfaces/IL1Bridge.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/bridge/interfaces/IL2Bridge.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/common/AllowList.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/common/AllowListed.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/common/L2ContractHelper.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/common/ReentrancyGuard.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/common/interfaces/IAllowList.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/common/libraries/UncheckedMath.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/common/libraries/UnsafeBytes.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/dev-contracts/ReturnSomething.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/dev-contracts/RevertFallback.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/dev-contracts/TestnetERC20Token.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/dev-contracts/test/DiamondCutTest.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/dev-contracts/test/DiamondProxyTest.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/dev-contracts/test/DummyERC20BytesTransferReturnValue.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/dev-contracts/test/DummyERC20NoTransferReturnValue.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/dev-contracts/test/GovernanceFacetTest.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/dev-contracts/test/MerkleTest.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/dev-contracts/test/PriorityQueueTest.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/dev-contracts/test/UnsafeBytesTest.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/zksync/Config.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/zksync/DiamondInit.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/zksync/DiamondProxy.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/zksync/Storage.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/zksync/Verifier.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/zksync/facets/Base.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/zksync/facets/DiamondCut.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/zksync/facets/Executor.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/zksync/facets/Getters.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/zksync/facets/Governance.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/zksync/facets/Mailbox.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/zksync/interfaces/IDiamondCut.sol::3 => pragma solidity ^0.8;

ethereum/contracts/zksync/interfaces/IExecutor.sol::3 => pragma solidity ^0.8;

ethereum/contracts/zksync/interfaces/IGetters.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/zksync/interfaces/IGovernance.sol::3 => pragma solidity ^0.8;

ethereum/contracts/zksync/interfaces/IMailbox.sol::3 => pragma solidity ^0.8;

ethereum/contracts/zksync/interfaces/IZkSync.sol::3 => pragma solidity ^0.8;

ethereum/contracts/zksync/libraries/Diamond.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/zksync/libraries/Merkle.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/zksync/libraries/PairingsBn254.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/zksync/libraries/PriorityQueue.sol::3 => pragma solidity ^0.8.0;

ethereum/contracts/zksync/libraries/TranscriptLib.sol::3 => pragma solidity ^0.8.0;

zksync/contracts/bridge/L2ERC20Bridge.sol::3 => pragma solidity ^0.8.0;

zksync/contracts/bridge/L2ETHBridge.sol::3 => pragma solidity ^0.8.0;

zksync/contracts/bridge/L2StandardERC20.sol::3 => pragma solidity ^0.8.0;

zksync/contracts/bridge/interfaces/IL1Bridge.sol::3 => pragma solidity ^0.8.0;

zksync/contracts/bridge/interfaces/IL2Bridge.sol::3 => pragma solidity ^0.8.0;

zksync/contracts/bridge/interfaces/IL2EthInitializable.sol::3 => pragma solidity ^0.8.0;

zksync/contracts/bridge/interfaces/IL2StandardToken.sol::3 => pragma solidity ^0.8.0;

zksync/contracts/ExternalDecoder.sol::3 => pragma solidity ^0.8.0;

zksync/contracts/L2ContractHelper.sol::3 => pragma solidity ^0.8.0;
```

## 2. `block.number` may exceed `type(uint32).max` (1 instance)

While this currently equates to around 1260 years, if there's a hard fork which makes block times much more frequent (e.g. to compete with Solana), then this limit may be reached much faster than expected, and transfers and delegations will remain stuck at their existing settings.

```solidity
ethereum/contracts/zksync/facets/Mailbox.sol::125 => uint64 expirationBlock = uint64(block.number + PRIORITY_EXPIRATION);
```
