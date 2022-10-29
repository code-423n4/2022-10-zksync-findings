

### [L01] require() should be used instead of assert()


#### Findings:
```
2022-10-zksync-main/ethereum/contracts/zksync/facets/DiamondCut.sol::16 => assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);
```



### [L02] Unsafe calls to optional ERC20 functions

#### Impact
decimals(), name() and symbol()
 are optional parts of the ERC20 specification, so there are tokens that
 do not implement them. It’s not safe to cast arbitrary token addresses 
in order to call these functions. If IERC20Metadata is to be relied on, that should be the variable type of the token variable, rather than it being address, so the compiler can verify that types correctly match, rather than this being a runtime failure. See [this](https://github.com/code-423n4/2021-05-yield-findings/issues/32) prior instance of this issue which was marked as Low risk. Do [this](https://github.com/boringcrypto/BoringSolidity/blob/c73ed73afa9273fbce93095ef177513191782254/contracts/libraries/BoringERC20.sol#L49-L55) to resolve the issue.
#### Findings:
```
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::114 => function name() public view override returns (string memory) {
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::120 => function symbol() public view override returns (string memory) {
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::126 => function decimals() public view override returns (uint8) {
```



### [L03] Missing checks for `address(0x0)` when assigning values to `address` state variables


#### Findings:
```
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::58 => zkSyncMailbox = _mailbox;
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::59 => allowList = _allowList;
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::79 => l2TokenFactory = _l2TokenFactory;
2022-10-zksync-main/ethereum/contracts/bridge/L1EthBridge.sol::49 => zkSyncMailbox = _mailbox;
2022-10-zksync-main/ethereum/contracts/bridge/L1EthBridge.sol::50 => allowList = _allowList;
2022-10-zksync-main/ethereum/contracts/common/AllowList.sol::34 => owner = _owner;
2022-10-zksync-main/ethereum/contracts/common/AllowList.sol::146 => pendingOwner = _newPendingOwner;
2022-10-zksync-main/ethereum/contracts/zksync/DiamondInit.sol::38 => s.verifier = _verifier;
2022-10-zksync-main/ethereum/contracts/zksync/DiamondInit.sol::39 => s.governor = _governor;
2022-10-zksync-main/ethereum/contracts/zksync/DiamondInit.sol::55 => s.allowList = _allowList;
2022-10-zksync-main/ethereum/contracts/zksync/DiamondInit.sol::56 => s.verifierParams = _verifierParams;
2022-10-zksync-main/ethereum/contracts/zksync/DiamondInit.sol::57 => s.zkPorterIsAvailable = _zkPorterIsAvailable;
2022-10-zksync-main/ethereum/contracts/zksync/DiamondInit.sol::58 => s.l2BootloaderBytecodeHash = _l2BootloaderBytecodeHash;
2022-10-zksync-main/ethereum/contracts/zksync/DiamondInit.sol::59 => s.l2DefaultAccountBytecodeHash = _l2DefaultAccountBytecodeHash;
2022-10-zksync-main/zksync/contracts/bridge/L2ERC20Bridge.sol::36 => l1Bridge = _l1Bridge;
2022-10-zksync-main/zksync/contracts/bridge/L2ERC20Bridge.sol::38 => l2TokenProxyBytecodeHash = _l2TokenProxyBytecodeHash;
2022-10-zksync-main/zksync/contracts/bridge/L2ETHBridge.sol::31 => l1Bridge = _l1Bridge;
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::46 => l1Address = _l1Address;
```



### [L04] `initialize` functions can be front-run

#### Impact
See [this](https://github.com/code-423n4/2021-10-badgerdao-findings/issues/40) finding from a prior badger-dao contest for details
#### Findings:
```
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::71 => function initialize(
2022-10-zksync-main/ethereum/contracts/bridge/L1EthBridge.sol::56 => function initialize(bytes calldata _l2BridgeBytecode) external reentrancyGuardInitializer {
2022-10-zksync-main/ethereum/contracts/zksync/DiamondInit.sol::25 => function initialize(
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::39 => constructor() initializer {}
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::43 => function bridgeInitialize(address _l1Address, bytes memory _data) external initializer {
```



### [L05] Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions

#### Impact
See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.
#### Findings:
```
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::11 => contract L2StandardERC20 is ERC20Upgradeable, IL2StandardToken {
```



### [L06] `_safeMint()` should be used rather than `_mint()` wherever possible

#### Impact
_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both OpenZeppelin and solmate have versions of this function
#### Findings:
```
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::103 => _mint(_to, _amount);
```






#####  Non-Critical Issues

### [N01] Adding a return statement when the function defines a named return variable, is redundant


#### Findings:
```
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::129 => return decimals_;
```



### [N02] `require()`/`revert()` statements should have descriptive reason strings


#### Findings:
```
2022-10-zksync-main/ethereum/contracts/bridge/L1EthBridge.sol::145 => require(amount != 0);
2022-10-zksync-main/ethereum/contracts/bridge/L1EthBridge.sol::221 => require(_message.length == 56);
2022-10-zksync-main/ethereum/contracts/bridge/L1EthBridge.sol::224 => require(bytes4(functionSignature) == this.finalizeWithdrawal.selector);
2022-10-zksync-main/ethereum/contracts/bridge/L1EthBridge.sol::238 => require(callSuccess);
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::43 => require(expectedNumberOfLayer1Txs == _newBlock.numberOfLayer1Txs);
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::45 => require(l2BlockTimestamp == _newBlock.timestamp);
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::297 => require(_recurisiveAggregationInput.length == 4);
2022-10-zksync-main/zksync/contracts/bridge/L2ETHBridge.sol::50 => require(_l1Token == CONVENTIONAL_ETH_ADDRESS);
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::96 => require(msg.sender == l2Bridge);
```



### [N03] constants should be defined rather than using magic numbers


#### Findings:
```
2022-10-zksync-main/ethereum/contracts/common/L2ContractHelper.sol::53 => require(bytecodeLenInWords < 2**16, "pp"); // bytecode length must be less than 2^16 words
2022-10-zksync-main/ethereum/contracts/common/L2ContractHelper.sol::72 => codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
2022-10-zksync-main/ethereum/contracts/zksync/Config.sol::12 => uint256 constant L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;
2022-10-zksync-main/ethereum/contracts/zksync/Config.sol::23 => uint256 constant INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + 64 * 4896;
2022-10-zksync-main/ethereum/contracts/zksync/Config.sol::26 => uint256 constant REPEATED_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + 40 * 7787;
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Merkle.sol::25 => require(_index < 2**pathLength, "pz");
```



### [N04] Use a more recent version of solidity

#### Impact
Use a solidity version of at least 0.8.12 to get string.concat() to be used instead of abi.encodePacked(<str>,<str>)
#### Findings:
```
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/bridge/L1EthBridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/bridge/interfaces/IL1Bridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/bridge/interfaces/IL2Bridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/common/AllowList.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/common/AllowListed.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/common/L2ContractHelper.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/common/ReentrancyGuard.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/common/interfaces/IAllowList.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/common/libraries/UncheckedMath.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/common/libraries/UnsafeBytes.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/Config.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/DiamondInit.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/DiamondProxy.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/Storage.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/facets/Base.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/facets/DiamondCut.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/facets/Getters.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/facets/Governance.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/facets/Mailbox.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol::3 => pragma solidity ^0.8;
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IExecutor.sol::3 => pragma solidity ^0.8;
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IGetters.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IGovernance.sol::3 => pragma solidity ^0.8;
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IMailbox.sol::3 => pragma solidity ^0.8;
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IZkSync.sol::3 => pragma solidity ^0.8;
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Merkle.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/libraries/PriorityQueue.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/zksync/contracts/ExternalDecoder.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/zksync/contracts/L2ContractHelper.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/zksync/contracts/bridge/L2ERC20Bridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/zksync/contracts/bridge/L2ETHBridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/zksync/contracts/bridge/interfaces/IL1Bridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/zksync/contracts/bridge/interfaces/IL2Bridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/zksync/contracts/bridge/interfaces/IL2EthInitializable.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/zksync/contracts/bridge/interfaces/IL2StandardToken.sol::3 => pragma solidity ^0.8.0;
```



### [N05] Event is missing `indexed` fields

#### Impact
Each event should use three indexed fields if there are three or more fields
#### Findings:
```

2022-10-zksync-main/ethereum/contracts/bridge/interfaces/IL1Bridge.sol::11 => event WithdrawalFinalized(address indexed to, address indexed l1Token, uint256 amount);
2022-10-zksync-main/ethereum/contracts/bridge/interfaces/IL1Bridge.sol::13 => event ClaimedFailedDeposit(address indexed to, address indexed l1Token, uint256 amount);
2022-10-zksync-main/ethereum/contracts/common/interfaces/IAllowList.sol::11 => event UpdatePublicAccess(address indexed target, bool newStatus);
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol::20 => event DiamondCutProposal(Diamond.FacetCut[] _facetCuts, address _initAddress);
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol::22 => event DiamondCutProposalCancelation(uint256 currentProposalId, bytes32 indexed proposedDiamondCutHash);
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IExecutor.sol::85 => event BlocksRevert(uint256 totalBlocksCommitted, uint256 totalBlocksVerified, uint256 totalBlocksExecuted);
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IGovernance.sol::35 => event ValidatorStatusUpdate(address indexed validatorAddress, bool isActive);
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IGovernance.sol::48 => event NewVerifierParams(VerifierParams oldVerifierParams, VerifierParams newVerifierParams);
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::16 => event DiamondCut(FacetCut[] facetCuts, address initAddress, bytes initCalldata);
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::12 => event BridgeInitialization(address indexed l1Token, string name, string symbol, uint8 decimals);
2022-10-zksync-main/zksync/contracts/bridge/interfaces/IL2StandardToken.sol::6 => event BridgeMint(address indexed _account, uint256 _amount);
2022-10-zksync-main/zksync/contracts/bridge/interfaces/IL2StandardToken.sol::8 => event BridgeBurn(address indexed _account, uint256 _amount);
```



### [N06] Use of sensitive/NC-inclusive terms


#### Findings:
```
2022-10-zksync-main/ethereum/contracts/bridge/L1EthBridge.sol::233 => bool callSuccess;
2022-10-zksync-main/ethereum/contracts/zksync/Storage.sol::41 => bool isService;
2022-10-zksync-main/ethereum/contracts/zksync/Storage.sol::104 => bool zkPorterIsAvailable;
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::104 => bool isSystemContextLogProcessed;
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::25 => bool isFreezable;
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::46 => bool isFrozen;
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::57 => bool isFreezable;
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::19 => bool ignoreName;
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::20 => bool ignoreSymbol;
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::21 => bool ignoreDecimals;
```



### [N07] Open TODOs

#### Impact
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment
#### Findings:
```
2022-10-zksync-main/ethereum/contracts/zksync/Config.sol::28 => // TODO: change constant to the real root hash of empty Merkle tree (SMA-184)
2022-10-zksync-main/ethereum/contracts/zksync/facets/Mailbox.sol::94 => // TODO: estimate gas for L1 execute
2022-10-zksync-main/ethereum/contracts/zksync/facets/Mailbox.sol::127 => // TODO: Restore after stable priority op fee modeling. (SMA-1230)
2022-10-zksync-main/ethereum/contracts/zksync/facets/Mailbox.sol::169 => layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IExecutor.sol::56 => /// TODO: The verifier integration is not finished yet, change the structure for compatibility later
```

### [N08] Unused file


#### Findings:
```
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/bridge/L1EthBridge.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/bridge/interfaces/IL1Bridge.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/bridge/interfaces/IL2Bridge.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/common/AllowList.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/common/AllowListed.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/common/L2ContractHelper.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/common/ReentrancyGuard.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/common/interfaces/IAllowList.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/common/libraries/UncheckedMath.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/common/libraries/UnsafeBytes.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/zksync/Config.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/zksync/DiamondInit.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/zksync/DiamondProxy.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/zksync/Storage.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/zksync/facets/Base.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/zksync/facets/DiamondCut.sol::1 => // SPDX-License-Identifier: MIT
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/zksync/facets/Getters.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/zksync/facets/Governance.sol::1 => // SPDX-License-Identifier: MIT
2022-10-zksync-main/ethereum/contracts/zksync/facets/Mailbox.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IExecutor.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IGetters.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IGovernance.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IMailbox.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IZkSync.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::1 => // SPDX-License-Identifier: MIT
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Merkle.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/ethereum/contracts/zksync/libraries/PriorityQueue.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/zksync/contracts/ExternalDecoder.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/zksync/contracts/L2ContractHelper.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/zksync/contracts/bridge/L2ERC20Bridge.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/zksync/contracts/bridge/L2ETHBridge.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/zksync/contracts/bridge/interfaces/IL1Bridge.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/zksync/contracts/bridge/interfaces/IL2Bridge.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/zksync/contracts/bridge/interfaces/IL2EthInitializable.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
2022-10-zksync-main/zksync/contracts/bridge/interfaces/IL2StandardToken.sol::1 => // SPDX-License-Identifier: MIT OR Apache-2.0
```



### [N09] `2**<n> - 1` should be re-written as `type(uint<n>).max`

#### Impact
Earlier versions of solidity can use uint<n>(-1) instead. Expressions not including the - 1 can often be re-written to accomodate the change (e.g. by using a > rather than a >=, which will also save some gas)
#### Findings:
```
2022-10-zksync-main/ethereum/contracts/common/L2ContractHelper.sol::53 => require(bytecodeLenInWords < 2**16, "pp"); // bytecode length must be less than 2^16 words
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Merkle.sol::25 => require(_index < 2**pathLength, "pz");
```





### [N10] `public` functions not called by the contract should be declared `external` instead

#### Impact
Contracts are allowed to override their parents’ functions and change the visibility from public to external .
#### Findings:
```
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::282 => function l2TokenAddress(address _l1Token) public view returns (address) {
```



### [N11] Numeric values having to do with time should use time units for readability

#### Impact
There are units for seconds, minutes, hours, days, and weeks
#### Findings:
```
2022-10-zksync-main/ethereum/contracts/common/AllowList.sol::14 => /// If the target contract is in the second list, then it is automatically available for calling any function.
2022-10-zksync-main/ethereum/contracts/zksync/Config.sol::47 => uint256 constant BLOCK_PERIOD = 13 seconds;
2022-10-zksync-main/ethereum/contracts/zksync/Config.sol::49 => /// @dev Expiration delta for priority request to be satisfied (in seconds)
2022-10-zksync-main/ethereum/contracts/zksync/Config.sol::51 => uint256 constant PRIORITY_EXPIRATION_PERIOD = 3 days;
2022-10-zksync-main/ethereum/contracts/zksync/Config.sol::58 => /// @dev Notice period before activation preparation status of upgrade mode (in seconds)
2022-10-zksync-main/ethereum/contracts/zksync/Config.sol::60 => uint256 constant UPGRADE_NOTICE_PERIOD = $$(defined(UPGRADE_NOTICE_PERIOD) ? UPGRADE_NOTICE_PERIOD : "14 days");
2022-10-zksync-main/ethereum/contracts/zksync/Config.sol::62 => /// @dev Timestamp - seconds since unix epoch
2022-10-zksync-main/ethereum/contracts/zksync/Config.sol::64 => defined(COMMIT_TIMESTAMP_NOT_OLDER) ? COMMIT_TIMESTAMP_NOT_OLDER : "365 days"
2022-10-zksync-main/ethereum/contracts/zksync/Config.sol::67 => /// @dev Maximum available error between real commit block timestamp and analog used in the verifier (in seconds)
2022-10-zksync-main/ethereum/contracts/zksync/Config.sol::68 => /// @dev Must be used cause miner's `block.timestamp` value can differ on some small value (as we know - 15 seconds)
2022-10-zksync-main/ethereum/contracts/zksync/Config.sol::70 => defined(COMMIT_TIMESTAMP_APPROXIMATION_DELTA) ? COMMIT_TIMESTAMP_APPROXIMATION_DELTA : "365 days"
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::67 => // We use the second option here.
```



### [N12] Constant redefined elsewhere

#### Impact
Consider defining in only one contract so that values cannot become out of sync when only one location is updated
#### Findings:
```
2022-10-zksync-main/ethereum/contracts/common/L2ContractHelper.sol::41 => bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
2022-10-zksync-main/zksync/contracts/L2ContractHelper.sol::24 => bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
```



### [N13] Non-library/interface files should use fixed compiler versions, not floating ones


#### Findings:
```
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/bridge/L1EthBridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/bridge/interfaces/IL1Bridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/bridge/interfaces/IL2Bridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/common/AllowList.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/common/AllowListed.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/common/L2ContractHelper.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/common/ReentrancyGuard.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/common/interfaces/IAllowList.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/common/libraries/UncheckedMath.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/common/libraries/UnsafeBytes.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/Config.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/DiamondInit.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/DiamondProxy.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/Storage.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/facets/Base.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/facets/DiamondCut.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/facets/Getters.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/facets/Governance.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/facets/Mailbox.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol::3 => pragma solidity ^0.8;
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IExecutor.sol::3 => pragma solidity ^0.8;
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IGetters.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IGovernance.sol::3 => pragma solidity ^0.8;
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IMailbox.sol::3 => pragma solidity ^0.8;
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IZkSync.sol::3 => pragma solidity ^0.8;
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Merkle.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/ethereum/contracts/zksync/libraries/PriorityQueue.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/zksync/contracts/ExternalDecoder.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/zksync/contracts/L2ContractHelper.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/zksync/contracts/bridge/L2ERC20Bridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/zksync/contracts/bridge/L2ETHBridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/zksync/contracts/bridge/interfaces/IL1Bridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/zksync/contracts/bridge/interfaces/IL2Bridge.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/zksync/contracts/bridge/interfaces/IL2EthInitializable.sol::3 => pragma solidity ^0.8.0;
2022-10-zksync-main/zksync/contracts/bridge/interfaces/IL2StandardToken.sol::3 => pragma solidity ^0.8.0;
```

### [N14] Interfaces should be moved to separate files

#### Impact
Most of the other interfaces in this project are in their own file in
 the interfaces directory. The interfaces below do not follow this 
pattern
#### Findings:
```
2022-10-zksync-main/ethereum/contracts/common/L2ContractHelper.sol::5 => interface IL2Messenger {
2022-10-zksync-main/ethereum/contracts/common/L2ContractHelper.sol::9 => interface IContractDeployer {
2022-10-zksync-main/zksync/contracts/L2ContractHelper.sol::5 => interface IL2Messenger {
2022-10-zksync-main/zksync/contracts/L2ContractHelper.sol::9 => interface IContractDeployer {
```




