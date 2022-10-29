## [L-01] Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

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
2022-10-zksync/ethereum/contracts/zksync/Storage.sol::3 => pragma solidity ^0.8.0;
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
2022-10-zksync/ethereum/contracts/zksync/libraries/PriorityQueue.sol::3 => pragma solidity ^0.8.0;
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

## [L-02] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::28 => s.diamondCutStorage.proposedDiamondCutTimestamp = block.timestamp;
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::52 => bool upgradeNoticePeriodPassed = block.timestamp >=
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::85 => s.diamondCutStorage.lastDiamondFreezeTimestamp = block.timestamp;
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::50 => bool timestampNotTooSmall = block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= l2BlockTimestamp;
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::51 => bool timestampNotTooBig = l2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA;
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::52 => require(timestampNotTooSmall, "h"); // New block timestamp is too small
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::53 => require(timestampNotTooBig, "h1"); // New block timestamp is too big
```

## [L-03] Open TODOs

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

```
2022-10-zksync/ethereum/contracts/zksync/Config.sol::28 => // TODO: change constant to the real root hash of empty Merkle tree (SMA-184)
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::94 => // TODO: estimate gas for L1 execute
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::127 => // TODO: Restore after stable priority op fee modeling. (SMA-1230)
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::169 => layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)
2022-10-zksync/ethereum/contracts/zksync/interfaces/IExecutor.sol::56 => /// TODO: The verifier integration is not finished yet, change the structure for compatibility later
```

## [L-04] require() should be used instead of assert()

require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking

```
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::16 => assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);
```

## [L-05] Events not emitted for important state changes

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

```
2022-10-zksync/ethereum/contracts/common/AllowList.sol::57 => function setPublicAccess(address _target, bool _enable) external onlyOwner {
2022-10-zksync/ethereum/contracts/common/AllowList.sol::89 => function setBatchPermissionToCall(
2022-10-zksync/ethereum/contracts/common/interfaces/IAllowList.sol::49 => function setBatchPublicAccess(address[] calldata _targets, bool[] calldata _enables) external;
2022-10-zksync/ethereum/contracts/common/interfaces/IAllowList.sol::51 => function setPublicAccess(address _target, bool _enable) external;
2022-10-zksync/ethereum/contracts/common/interfaces/IAllowList.sol::53 => function setBatchPermissionToCall(
2022-10-zksync/ethereum/contracts/common/interfaces/IAllowList.sol::60 => function setPermissionToCall(
2022-10-zksync/ethereum/contracts/common/interfaces/IAllowList.sol::67 => function setPendingOwner(address _newPendingOwner) external;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IGovernance.sol::9 => function setPendingGovernor(address _newPendingGovernor) external;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IGovernance.sol::13 => function setValidator(address _validator, bool _active) external;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IGovernance.sol::19 => function setPorterAvailability(bool _isPorterAvailable) external;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IGovernance.sol::21 => function setVerifier(Verifier _newVerifier) external;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IGovernance.sol::23 => function setVerifierParams(VerifierParams calldata _newVerifierParams) external;
```

## [L-06] _safeMint() should be used rather than _mint() wherever possible

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol::103 => _mint(_to, _amount);
```

## [L-07] Upgradeable contract is missing a __gap[50] storage variable to allow for new storage variables in later versions

__gap is empty reserved space in storage that is recommended to be put in place in upgradeable contracts. It allows new state variables to be added in the future without compromising the storage compatibility with existing deployments

```
2022-10-zksync/zksync/contracts/bridge/L2ERC20Bridge.sol::6 => import "@openzeppelin/contracts/proxy/beacon/UpgradeableBeacon.sol";
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol::5 => import "@openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol";
```

## [N-01] Use a more recent version of solidity

Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

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
2022-10-zksync/ethereum/contracts/zksync/Storage.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/Base.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/Getters.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IGetters.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/libraries/Merkle.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/libraries/PriorityQueue.sol::3 => pragma solidity ^0.8.0;
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

## [N-02] require()/revert() statements should have descriptive reason strings

Descriptive reason strings should be used so that users can troubleshot any reverted calls

```
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::145 => require(amount != 0);
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::221 => require(_message.length == 56);
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::224 => require(bytes4(functionSignature) == this.finalizeWithdrawal.selector);
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::238 => require(callSuccess);
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::43 => require(expectedNumberOfLayer1Txs == _newBlock.numberOfLayer1Txs);
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::45 => require(l2BlockTimestamp == _newBlock.timestamp);
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::297 => require(_recurisiveAggregationInput.length == 4);
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::109 => revert("C"); // undefined diamond cut action
2022-10-zksync/zksync/contracts/bridge/L2ETHBridge.sol::50 => require(_l1Token == CONVENTIONAL_ETH_ADDRESS);
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol::96 => require(msg.sender == l2Bridge);
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol::116 => if (availableGetters.ignoreName) revert();
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol::122 => if (availableGetters.ignoreSymbol) revert();
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol::128 => if (availableGetters.ignoreDecimals) revert();
```

## [N-03] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

```
2022-10-zksync/ethereum/contracts/zksync/interfaces/IDiamondCut.sol::20 => event DiamondCutProposal(Diamond.FacetCut[] _facetCuts, address _initAddress);
2022-10-zksync/ethereum/contracts/zksync/interfaces/IDiamondCut.sol::24 => event DiamondCutProposalExecution(Diamond.DiamondCutData _diamondCut);
2022-10-zksync/ethereum/contracts/zksync/interfaces/IDiamondCut.sol::26 => event EmergencyFreeze();
2022-10-zksync/ethereum/contracts/zksync/interfaces/IDiamondCut.sol::28 => event Unfreeze(uint256 lastDiamondFreezeTimestamp);
2022-10-zksync/ethereum/contracts/zksync/interfaces/IExecutor.sol::85 => event BlocksRevert(uint256 totalBlocksCommitted, uint256 totalBlocksVerified, uint256 totalBlocksExecuted);
2022-10-zksync/ethereum/contracts/zksync/interfaces/IGovernance.sol::32 => event IsPorterAvailableStatusUpdate(bool isPorterAvailable);
2022-10-zksync/ethereum/contracts/zksync/interfaces/IGovernance.sol::48 => event NewVerifierParams(VerifierParams oldVerifierParams, VerifierParams newVerifierParams);
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::16 => event DiamondCut(FacetCut[] facetCuts, address initAddress, bytes initCalldata);
```

## [N-04] Missing NatSpec

Code should include NatSpec

```
2022-10-zksync/ethereum/contracts/common/libraries/UncheckedMath.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync/ethereum/contracts/zksync/interfaces/IDiamondCut.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync/ethereum/contracts/zksync/interfaces/IZkSync.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync/zksync/contracts/L2ContractHelper.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync/zksync/contracts/bridge/interfaces/IL2EthInitializable.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync/zksync/contracts/bridge/interfaces/IL2StandardToken.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
```

## [N-05] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public.

```
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::243 => function l2TokenAddress(address) public pure returns (address) {
2022-10-zksync/zksync/contracts/bridge/L2ETHBridge.sol::79 => function l2TokenAddress(address) public pure returns (address) {
2022-10-zksync/zksync/contracts/bridge/L2ETHBridge.sol::84 => function l1TokenAddress(address) public pure override returns (address) {
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol::126 => function decimals() public view override returns (uint8) {
```

## [N-06] Missing event for critical parameter change

Emitting events after sensitive changes take place, to facilitate tracking and notify off-chain clients following changes to the contract.

```
2022-10-zksync/ethereum/contracts/common/AllowList.sol::57 => function setPublicAccess(address _target, bool _enable) external onlyOwner {
2022-10-zksync/ethereum/contracts/common/AllowList.sol::89 => function setBatchPermissionToCall(
2022-10-zksync/ethereum/contracts/common/interfaces/IAllowList.sol::49 => function setBatchPublicAccess(address[] calldata _targets, bool[] calldata _enables) external;
2022-10-zksync/ethereum/contracts/common/interfaces/IAllowList.sol::51 => function setPublicAccess(address _target, bool _enable) external;
2022-10-zksync/ethereum/contracts/common/interfaces/IAllowList.sol::53 => function setBatchPermissionToCall(
2022-10-zksync/ethereum/contracts/common/interfaces/IAllowList.sol::60 => function setPermissionToCall(
2022-10-zksync/ethereum/contracts/common/interfaces/IAllowList.sol::67 => function setPendingOwner(address _newPendingOwner) external;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IGovernance.sol::9 => function setPendingGovernor(address _newPendingGovernor) external;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IGovernance.sol::13 => function setValidator(address _validator, bool _active) external;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IGovernance.sol::19 => function setPorterAvailability(bool _isPorterAvailable) external;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IGovernance.sol::21 => function setVerifier(Verifier _newVerifier) external;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IGovernance.sol::23 => function setVerifierParams(VerifierParams calldata _newVerifierParams) external;
```

## [N-07] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

instances:

```
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::41 => bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
2022-10-zksync/ethereum/contracts/common/ReentrancyGuard.sol::27 => uint256 private constant LOCK_FLAG_ADDRESS = 0x8e94fed44239eb2314ab7a406345e6c5a8f0ccedf3b600de3d004e672c33abf4; // keccak256("ReentrancyGuard") - 1;
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::14 => bytes32 constant DIAMOND_STORAGE_POSITION = 0xc8fcad8db84d3cc18b4c41d551ea0ee66dd599cde068d998e57d5e09332c131b; // keccak256("diamond.standard.diamond.storage") - 1;
2022-10-zksync/zksync/contracts/L2ContractHelper.sol::24 => bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
```

## [N-08] Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

```
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::104 => /// @param _diamondCutHash The hash of the diamond cut that security council members want to approve. Needed to prevent unintentional approvals, including reorg attacks
2022-10-zksync/ethereum/contracts/zksync/interfaces/IMailbox.sol::15 => /// @param maxFeePerErg The absolute maximum sender willing to pay per unit of ergs to get the transaction included in a block. Analog to the EIP-1559 `maxFeePerGas` on an L1 transactions
2022-10-zksync/ethereum/contracts/zksync/interfaces/IMailbox.sol::16 => /// @param maxPriorityFeePerErg The additional fee that is paid directly to the validator to incentivize them to include the transaction in a block. Analog to the EIP-1559 `maxPriorityFeePerGas` on an L1 transactions
2022-10-zksync/ethereum/contracts/zksync/interfaces/IMailbox.sol::17 => /// @param paymaster The address of the EIP-4337 paymaster, that will pay fees for the transaction. `uint256` type for possible address format changes and maintaining backward compatibility
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::14 => bytes32 constant DIAMOND_STORAGE_POSITION = 0xc8fcad8db84d3cc18b4c41d551ea0ee66dd599cde068d998e57d5e09332c131b; // keccak256("diamond.standard.diamond.storage") - 1;
```
