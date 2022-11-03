# USE OF FLOATING PRAGMA

## Issue Details
Solidity source files indicate the versions of the compiler they can be compiled with using a pragma directive at the top of the solidity file. This can either be a floating pragma or a specific compiler version.
The contract was found to be using a floating pragma which is not considered safe as it can be compiled with all the versions described.
The following affected files were found to be using floating pragma:
```
/ethereum/contracts/common/AllowListed.sol - ^0.8.0
/ethereum/contracts/zksync/facets/Base.sol - ^0.8.0
/zksync/contracts/bridge/interfaces/IL1Bridge.sol - ^0.8.0
/zksync/contracts/ExternalDecoder.sol - ^0.8.0
/zksync/contracts/L2ContractHelper.sol - ^0.8.0
/zksync/contracts/bridge/L2ERC20Bridge.sol - ^0.8.0
/ethereum/contracts/zksync/DiamondInit.sol - ^0.8.0
/zksync/contracts/bridge/interfaces/IL2StandardToken.sol - ^0.8.0
/zksync/contracts/bridge/L2ETHBridge.sol - ^0.8.0
/ethereum/contracts/zksync/libraries/PairingsBn254.sol - ^0.8.0
/ethereum/contracts/zksync/libraries/Merkle.sol - ^0.8.0
/ethereum/contracts/zksync/Storage.sol - ^0.8.0
/ethereum/contracts/zksync/facets/Getters.sol - ^0.8.0
/zksync/contracts/bridge/interfaces/IL2Bridge.sol - ^0.8.0
/ethereum/contracts/zksync/DiamondProxy.sol - ^0.8.0
/ethereum/contracts/zksync/facets/Governance.sol - ^0.8.0
/ethereum/contracts/zksync/facets/Mailbox.sol - ^0.8.0
/zksync/contracts/bridge/interfaces/IL2EthInitializable.sol - ^0.8.0
/zksync/contracts/bridge/L2StandardERC20.sol - ^0.8.0
/ethereum/contracts/zksync/Verifier.sol - ^0.8.0
/ethereum/contracts/zksync/interfaces/IGetters.sol - ^0.8.0
/ethereum/contracts/zksync/facets/DiamondCut.sol - ^0.8.0
/ethereum/contracts/zksync/facets/Executor.sol - ^0.8.0
/ethereum/contracts/zksync/libraries/Diamond.sol - ^0.8.0
/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol - ^0.8.0
/ethereum/contracts/zksync/libraries/TranscriptLib.sol - ^0.8.0
/ethereum/contracts/common/AllowList.sol - ^0.8.0
/ethereum/contracts/dev-contracts/ReturnSomething.sol - ^0.8.0
/ethereum/contracts/zksync/libraries/PriorityQueue.sol - ^0.8.0
/ethereum/contracts/dev-contracts/test/UnsafeBytesTest.sol - ^0.8.0
/ethereum/contracts/dev-contracts/test/DiamondProxyTest.sol - ^0.8.0
/ethereum/contracts/bridge/L1EthBridge.sol - ^0.8.0
/ethereum/contracts/common/libraries/UncheckedMath.sol - ^0.8.0
/ethereum/contracts/dev-contracts/test/GovernanceFacetTest.sol - ^0.8.0
/ethereum/contracts/dev-contracts/test/PriorityQueueTest.sol - ^0.8.0
/ethereum/contracts/common/AllowListed.sol - ^0.8.0
/ethereum/contracts/common/interfaces/IAllowList.sol - ^0.8.0
/ethereum/contracts/common/libraries/UnsafeBytes.sol - ^0.8.0
/ethereum/contracts/common/ReentrancyGuard.sol - ^0.8.0
/ethereum/contracts/dev-contracts/TestnetERC20Token.sol - ^0.8.0
/ethereum/contracts/bridge/interfaces/IL2Bridge.sol - ^0.8.0
/ethereum/contracts/dev-contracts/test/DummyERC20BytesTransferReturnValue.sol - ^0.8.0
/ethereum/contracts/dev-contracts/test/DummyERC20NoTransferReturnValue.sol - ^0.8.0
/ethereum/contracts/dev-contracts/RevertFallback.sol - ^0.8.0
/ethereum/contracts/dev-contracts/test/DiamondCutTest.sol - ^0.8.0
/ethereum/contracts/dev-contracts/test/MerkleTest.sol - ^0.8.0
/ethereum/contracts/bridge/interfaces/IL1Bridge.sol - ^0.8.0
/ethereum/contracts/bridge/L1ERC20Bridge.sol - ^0.8.0
/ethereum/contracts/common/L2ContractHelper.sol - ^0.8.0
```

## Remediation
It is recommended to use a fixed pragma version, as future compiler versions may handle certain language constructions in a way the developer did not foresee.
Using a floating pragma may introduce several vulnerabilities if compiled with an older version.
The developers should always use the exact Solidity compiler version when designing their contracts as it may break the changes in the future.
Instead of `^0.8.0` use pragma solidity `0.8.7`, which is a stable and recommended version right now.