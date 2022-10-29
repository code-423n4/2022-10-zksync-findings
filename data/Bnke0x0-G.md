

### [G01] State variables only set in the constructor should be declared `immutable`

#### Impact
Avoids a Gsset (20000 gas)
#### Findings:
```
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::47 => address public l2Bridge;
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::50 => address public l2TokenFactory;
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::53 => bytes32 public l2ProxyTokenBytecodeHash;
2022-10-zksync-main/ethereum/contracts/bridge/L1EthBridge.sol::44 => address public l2Bridge;
2022-10-zksync-main/ethereum/contracts/common/AllowList.sol::20 => address public pendingOwner;
2022-10-zksync-main/ethereum/contracts/common/AllowList.sol::23 => address public owner;
2022-10-zksync-main/zksync/contracts/bridge/L2ERC20Bridge.sol::23 => UpgradeableBeacon public l2TokenFactory;
2022-10-zksync-main/zksync/contracts/bridge/L2ETHBridge.sol::16 => uint256 public totalSupply;
2022-10-zksync-main/zksync/contracts/bridge/L2ETHBridge.sol::22 => address public override l1Bridge;
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::29 => uint8 private decimals_;
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::32 => address public override l2Bridge;
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::35 => address public override l1Address;
```




### [G02] State variables can be packed into fewer storage slots

#### Impact
If variables occupying the same slot are both written the same 
function or by the constructor avoids a separate Gsset (20000 gas). 
Reads of the variables are also cheaper
#### Findings:
```
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::47 => address public l2Bridge;
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::50 => address public l2TokenFactory;
2022-10-zksync-main/ethereum/contracts/bridge/L1EthBridge.sol::44 => address public l2Bridge;
2022-10-zksync-main/ethereum/contracts/common/AllowList.sol::20 => address public pendingOwner;
2022-10-zksync-main/ethereum/contracts/common/AllowList.sol::23 => address public owner;
```




### [G03] `<array>.length` should not be looked up in every loop of a `for` loop

#### Impact
Even memory arrays incur the overhead of bit tests and bit shifts to 
calculate the array length. Storage array length checks incur an extra 
Gwarmaccess (100 gas) PER-LOOP.
#### Findings:
```
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::111 => for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
```




### [G04] `++i/i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for` and `while` loops


#### Findings:
```
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::94 => for (uint256 i = 0; i < facetCutsLength; ++i) {
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::132 => for (uint256 i = 0; i < selectorsLength; ++i) {
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::153 => for (uint256 i = 0; i < selectorsLength; ++i) {
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::173 => for (uint256 i = 0; i < selectorsLength; ++i) {
```


### [G05] Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement


#### Findings:
```
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::117 => require(amount > 0, "1T"); // empty deposit amount
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::210 => require(amount > 0, "y1");
2022-10-zksync-main/ethereum/contracts/zksync/facets/DiamondCut.sol::16 => assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::100 => require(selectors.length > 0, "B"); // no functions for diamond cut
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Merkle.sol::23 => require(pathLength > 0, "xc");
```




### [G06] It costs more gas to initialize variables to zero than to let the default of zero be applied


#### Findings:
```
2022-10-zksync-main/ethereum/contracts/common/AllowList.sol::69 => for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {
2022-10-zksync-main/ethereum/contracts/common/AllowList.sol::103 => for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::111 => for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::162 => for (uint256 i = 0; i < blocksLength; i = i.uncheckedInc()) {
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::180 => for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::210 => for (uint256 i = 0; i < nBlocks; i = i.uncheckedInc()) {
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::240 => for (uint256 i = 0; i < committedBlocksLength; i = i.uncheckedInc()) {
2022-10-zksync-main/ethereum/contracts/zksync/facets/Getters.sol::163 => for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
2022-10-zksync-main/ethereum/contracts/zksync/facets/Mailbox.sol::223 => for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::94 => for (uint256 i = 0; i < facetCutsLength; ++i) {
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::132 => for (uint256 i = 0; i < selectorsLength; ++i) {
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::153 => for (uint256 i = 0; i < selectorsLength; ++i) {
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::173 => for (uint256 i = 0; i < selectorsLength; ++i) {
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Merkle.sol::28 => for (uint256 i = 0; i < pathLength; i = i.uncheckedInc()) {
```





### [G07] Splitting `require()` statements that use `&&` Cost gas

#### Impact
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) for an example
#### Findings:
```
2022-10-zksync-main/ethereum/contracts/common/L2ContractHelper.sol::65 => require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
```




### [G08] Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`

#### Impact
See [this issue](https://github.com/ethereum/solidity/issues/9232) for a detailed description of the issue
#### Findings:
```
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::283 => bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));
2022-10-zksync-main/ethereum/contracts/common/L2ContractHelper.sol::41 => bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
2022-10-zksync-main/ethereum/contracts/common/L2ContractHelper.sol::82 => bytes32 data = keccak256(
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::355 => bytes32 passThroughDataHash = keccak256(_blockPassThroughData(_newBlockData));
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::356 => bytes32 metadataHash = keccak256(_blockMetaParameters());
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::357 => bytes32 auxiliaryOutputHash = keccak256(_blockAuxilaryOutput(_newBlockData));
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::377 => bytes32 initialStorageChangesHash = keccak256(_block.initialStorageChanges);
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::378 => bytes32 repeatedStorageChangesHash = keccak256(_block.repeatedStorageChanges);
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::379 => bytes32 l2ToL1LogsHash = keccak256(_block.l2Logs);
2022-10-zksync-main/ethereum/contracts/zksync/facets/Mailbox.sol::58 => bytes32 hashedLog = keccak256(
2022-10-zksync-main/zksync/contracts/L2ContractHelper.sol::24 => bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
2022-10-zksync-main/zksync/contracts/L2ContractHelper.sol::37 => bytes32 data = keccak256(
2022-10-zksync-main/zksync/contracts/bridge/L2ERC20Bridge.sol::114 => bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));
```


### [G09] `require()` or `revert()` statements that check input arguments should be at the top of the function

#### Impact
Checks that involve constants should come before checks that involve state variables
#### Findings:
```
2022-10-zksync-main/ethereum/contracts/common/AllowList.sol::33 => require(_owner != address(0), "kq");
2022-10-zksync-main/ethereum/contracts/zksync/DiamondProxy.sol::29 => require(facetAddress != address(0), "F"); // Proxy has no facet for this selector
2022-10-zksync-main/ethereum/contracts/zksync/facets/Getters.sol::148 => require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::126 => require(_facet != address(0), "G"); // facet with zero address cannot be added
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::150 => require(_facet != address(0), "K"); // cannot replace facet with zero address
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::156 => require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::176 => require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet
2022-10-zksync-main/zksync/contracts/bridge/L2ERC20Bridge.sol::95 => require(l1Token != address(0), "yh");
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::45 => require(_l1Address != address(0), "in6"); // Should be non-zero address
```



### [G10] Functions guaranteed to revert when called by normal users can be marked `payable`

#### Impact
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
#### Findings:
```
2022-10-zksync-main/ethereum/contracts/common/AllowList.sol::57 => function setPublicAccess(address _target, bool _enable) external onlyOwner {
2022-10-zksync-main/ethereum/contracts/common/AllowList.sol::65 => function setBatchPublicAccess(address[] calldata _targets, bool[] calldata _enables) external onlyOwner {
2022-10-zksync-main/ethereum/contracts/common/AllowList.sol::140 => function setPendingOwner(address _newPendingOwner) external onlyOwner {
2022-10-zksync-main/ethereum/contracts/zksync/facets/DiamondCut.sol::22 => function proposeDiamondCut(Diamond.FacetCut[] calldata _facetCuts, address _initAddress) external onlyGovernor {
2022-10-zksync-main/ethereum/contracts/zksync/facets/DiamondCut.sol::35 => function cancelDiamondCutProposal() external onlyGovernor {
2022-10-zksync-main/ethereum/contracts/zksync/facets/DiamondCut.sol::46 => function executeDiamondCutProposal(Diamond.DiamondCutData calldata _diamondCut) external onlyGovernor {
2022-10-zksync-main/ethereum/contracts/zksync/facets/DiamondCut.sol::78 => function emergencyFreezeDiamond() external onlyGovernor {
2022-10-zksync-main/ethereum/contracts/zksync/facets/DiamondCut.sol::91 => function unfreezeDiamond() external onlyGovernor {
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::208 => function executeBlocks(StoredBlockInfo[] calldata _blocksData) external nonReentrant onlyValidator {
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::336 => function revertBlocks(uint256 _newLastBlock) external nonReentrant onlyValidator {
2022-10-zksync-main/ethereum/contracts/zksync/facets/Governance.sol::15 => function setPendingGovernor(address _newPendingGovernor) external onlyGovernor {
2022-10-zksync-main/ethereum/contracts/zksync/facets/Governance.sol::45 => function setValidator(address _validator, bool _active) external onlyGovernor {
2022-10-zksync-main/ethereum/contracts/zksync/facets/Governance.sol::54 => function setL2BootloaderBytecodeHash(bytes32 _l2BootloaderBytecodeHash) external onlyGovernor {
2022-10-zksync-main/ethereum/contracts/zksync/facets/Governance.sol::69 => function setL2DefaultAccountBytecodeHash(bytes32 _l2DefaultAccountBytecodeHash) external onlyGovernor {
2022-10-zksync-main/ethereum/contracts/zksync/facets/Governance.sol::84 => function setPorterAvailability(bool _zkPorterIsAvailable) external onlyGovernor {
2022-10-zksync-main/ethereum/contracts/zksync/facets/Governance.sol::94 => function setVerifier(Verifier _newVerifier) external onlyGovernor {
2022-10-zksync-main/ethereum/contracts/zksync/facets/Governance.sol::104 => function setVerifierParams(VerifierParams calldata _newVerifierParams) external onlyGovernor {
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::102 => function bridgeMint(address _to, uint256 _amount) external override onlyBridge {
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::109 => function bridgeBurn(address _from, uint256 _amount) external override onlyBridge {
```




### [G11] Use a more recent version of solidity

#### Impact
Use a solidity version of at least 0.8.10 to have external calls skip
 contract existence checks if the external call has a return value
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


### [G12] Use `calldata` instead of `memory` for function parameters

#### Impact
Use calldata instead of memory for function parameters. Having function arguments use calldata instead of memory can save gas.
#### Findings:
```
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::260 => function _parseL2WithdrawalMessage(bytes memory _l2ToL1message)
2022-10-zksync-main/ethereum/contracts/bridge/L1EthBridge.sol::214 => function _parseL2WithdrawalMessage(bytes memory _message)
2022-10-zksync-main/ethereum/contracts/common/L2ContractHelper.sol::6 => function sendToL1(bytes memory _message) external returns (bytes32);
2022-10-zksync-main/ethereum/contracts/common/L2ContractHelper.sol::43 => function sendMessageToL1(bytes memory _message) internal returns (bytes32) {
2022-10-zksync-main/ethereum/contracts/common/L2ContractHelper.sol::47 => function hashL2Bytecode(bytes memory _bytecode) internal pure returns (bytes32 hashedBytecode) {
2022-10-zksync-main/ethereum/contracts/common/libraries/UnsafeBytes.sol::18 => function readUint32(bytes memory _bytes, uint256 _start) internal pure returns (uint32 result, uint256 offset) {
2022-10-zksync-main/ethereum/contracts/common/libraries/UnsafeBytes.sol::25 => function readAddress(bytes memory _bytes, uint256 _start) internal pure returns (address result, uint256 offset) {
2022-10-zksync-main/ethereum/contracts/common/libraries/UnsafeBytes.sol::32 => function readUint256(bytes memory _bytes, uint256 _start) internal pure returns (uint256 result, uint256 offset) {
2022-10-zksync-main/ethereum/contracts/common/libraries/UnsafeBytes.sol::39 => function readBytes32(bytes memory _bytes, uint256 _start) internal pure returns (bytes32 result, uint256 offset) {
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::23 => function _commitOneBlock(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::26 => returns (StoredBlockInfo memory storedNewBlock)
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::80 => function _calculateBlockHash(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::152 => function commitBlocks(StoredBlockInfo memory _lastCommittedBlockData, CommitBlockInfo[] calldata _newBlocksData)
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::190 => function _executeOneBlock(StoredBlockInfo memory _storedBlock, uint256 _executedBlockIdx) internal {
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::385 => function _hashStoredBlockInfo(StoredBlockInfo memory _storedBlockInfo) internal pure returns (bytes32) {
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::89 => function diamondCut(DiamondCutData memory _diamondCut) internal {
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::167 => function _removeFunctions(address _facet, bytes4[] memory _selectors) private {
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::175 => SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::277 => function _initializeDiamondCut(address _init, bytes memory _calldata) private {
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Diamond.sol::282 => (bool success, bytes memory data) = _init.delegatecall(_calldata);
2022-10-zksync-main/ethereum/contracts/zksync/libraries/PriorityQueue.sol::54 => function pushBack(Queue storage _queue, PriorityOperation memory _operation) internal {
2022-10-zksync-main/zksync/contracts/ExternalDecoder.sol::10 => function decodeString(bytes memory _input) external pure returns (string memory result) {
2022-10-zksync-main/zksync/contracts/ExternalDecoder.sol::15 => function decodeUint8(bytes memory _input) external pure returns (uint8 result) {
2022-10-zksync-main/zksync/contracts/L2ContractHelper.sol::6 => function sendToL1(bytes memory _message) external returns (bytes32);
2022-10-zksync-main/zksync/contracts/L2ContractHelper.sol::26 => function sendMessageToL1(bytes memory _message) internal returns (bytes32) {
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::43 => function bridgeInitialize(address _l1Address, bytes memory _data) external initializer {
```



### [G13] Multiple `address` mappings can be combined into a single `mapping` of an `address` to a `struct`, where appropriate

#### Impact
Saves a storage slot for the mapping. Depending on the circumstances 
and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. 
Reads and subsequent writes can also be cheaper when a function requires
 both values and they both fit in the same storage slot
#### Findings:
```
2022-10-zksync-main/ethereum/contracts/zksync/Storage.sol::22 => mapping(address => bool) securityCouncilMembers;
2022-10-zksync-main/ethereum/contracts/zksync/Storage.sol::77 => mapping(address => bool) validators;
2022-10-zksync-main/ethereum/contracts/zksync/Storage.sol::87 => mapping(uint256 => bytes32) storedBlockHashes;
2022-10-zksync-main/ethereum/contracts/zksync/Storage.sol::89 => mapping(uint256 => bytes32) l2LogsRootHashes;
```


### [G14] Using `bools` for storage incurs overhead


#### Findings:
```
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::40 => mapping(uint256 => mapping(uint256 => bool)) public isWithdrawalFinalized;
2022-10-zksync-main/ethereum/contracts/bridge/L1EthBridge.sol::37 => mapping(uint256 => mapping(uint256 => bool)) public isWithdrawalFinalized;
2022-10-zksync-main/ethereum/contracts/common/AllowList.sol::26 => mapping(address => bool) public isAccessPublic;
2022-10-zksync-main/ethereum/contracts/common/AllowList.sol::30 => mapping(address => mapping(address => mapping(bytes4 => bool))) public hasSpecialAccessToCall;
2022-10-zksync-main/ethereum/contracts/zksync/Storage.sol::22 => mapping(address => bool) securityCouncilMembers;
2022-10-zksync-main/ethereum/contracts/zksync/Storage.sol::77 => mapping(address => bool) validators;
```


### [G15] Usage of `assert()` instead of `require()`

#### Impact
Between solc 0.4.10 and 0.8.0, require() used REVERT (0xfd) opcode which refunded remaining gas on failure while assert() used INVALID (0xfe) opcode which consumed all the supplied gas. (see https://docs.soliditylang.org/en/v0.8.1/control-structures.html#error-handling-assert-require-revert-and-exceptions).require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking (properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix).
From the current usage of assert, my guess is that they can be replaced with require, unless a Panic really is intended.
#### Findings:
```
2022-10-zksync-main/ethereum/contracts/zksync/facets/DiamondCut.sol::16 => assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);
```



### [G16] Empty blocks should be removed or emit something

#### Impact
The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be abstract and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})
#### Findings:
```
2022-10-zksync-main/ethereum/contracts/zksync/DiamondInit.sol::15 => constructor() reentrancyGuardInitializer {}
2022-10-zksync-main/ethereum/contracts/zksync/interfaces/IZkSync.sol::11 => interface IZkSync is IMailbox, IGovernance, IExecutor, IDiamondCut, IGetters {}
2022-10-zksync-main/zksync/contracts/bridge/L2StandardERC20.sol::39 => constructor() initializer {}
```


### [G17] `abi.encode()` is less efficient than abi.encodePacked()


#### Findings:
```
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::82 => bytes memory create2Input = abi.encode(address(this), l2ProxyTokenBytecodeHash, _governor);
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::168 => data = abi.encode(data1, data2, data3);
2022-10-zksync-main/ethereum/contracts/bridge/L1ERC20Bridge.sol::283 => bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));
2022-10-zksync-main/ethereum/contracts/bridge/L1EthBridge.sol::58 => bytes memory create2Input = abi.encode(address(this));
2022-10-zksync-main/ethereum/contracts/zksync/DiamondInit.sol::54 => s.storedBlockHashes[0] = keccak256(abi.encode(storedBlockZero));
2022-10-zksync-main/ethereum/contracts/zksync/facets/DiamondCut.sol::27 => s.diamondCutStorage.proposedDiamondCutHash = keccak256(abi.encode(_facetCuts, _initAddress));
2022-10-zksync-main/ethereum/contracts/zksync/facets/DiamondCut.sol::61 => keccak256(abi.encode(_diamondCut.facetCuts, _diamondCut.initAddress)),
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::85 => return keccak256(abi.encode(_previousBlock.blockHash, _newBlock.newStateRoot));
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::122 => chainedPriorityTxsHash = keccak256(abi.encode(chainedPriorityTxsHash, canonicalTxHash));
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::182 => concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::359 => return keccak256(abi.encode(passThroughDataHash, metadataHash, auxiliaryOutputHash));
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::381 => return abi.encode(_block.l2LogsTreeRoot, l2ToL1LogsHash, initialStorageChangesHash, repeatedStorageChangesHash);
2022-10-zksync-main/ethereum/contracts/zksync/facets/Executor.sol::386 => return keccak256(abi.encode(_storedBlockInfo));
2022-10-zksync-main/ethereum/contracts/zksync/facets/Mailbox.sol::163 => canonicalTxHash = keccak256(abi.encode(transaction));
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Merkle.sol::30 => currentHash = keccak256(abi.encode(currentHash, _path[i]));
2022-10-zksync-main/ethereum/contracts/zksync/libraries/Merkle.sol::32 => currentHash = keccak256(abi.encode(_path[i], currentHash));
2022-10-zksync-main/zksync/contracts/bridge/L2ERC20Bridge.sol::114 => bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));
```


### [G18] Optimize names to save gas

#### Impact
public/external function names and public member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9)
 link for an example of how it works. Below are the interfaces/abstract 
contracts that can be optimized so that the most frequently-called 
functions use the least amount of gas possible during method lookup. 
Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)
#### Findings:
```
2022-10-zksync-main/ethereum/contracts/common/AllowListed.sol::8 => abstract contract AllowListed {
2022-10-zksync-main/ethereum/contracts/common/ReentrancyGuard.sol::24 => abstract contract ReentrancyGuard {
```






