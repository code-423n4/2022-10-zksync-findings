# Low

## No indexed event parameters
**Summary**: None of Sherlock’s events use indexed parameters. This could make it harder and inefficient for off-chain tools to analyse them.
**Details**: Indexed parameters (“topics”) are searchable event parameters. They are stored separately from unindexed event parameters in an efficient manner to allow for faster access. This is useful for efficient off-chain-analysis, but it is also more costly gas-wise.
```solidity
ethereum/contracts/zksync/libraries/Diamond.sol:16:    event DiamondCut(FacetCut[] facetCuts, address initAddress, bytes initCalldata);
ethereum/contracts/zksync/interfaces/IMailbox.sol:95:    event NewPriorityRequest(
ethereum/contracts/zksync/interfaces/IGovernance.sol:32:    event IsPorterAvailableStatusUpdate(bool isPorterAvailable);
ethereum/contracts/zksync/interfaces/IGovernance.sol:48:    event NewVerifierParams(VerifierParams oldVerifierParams, VerifierParams newVerifierParams);
ethereum/contracts/zksync/interfaces/IDiamondCut.sol:20:    event DiamondCutProposal(Diamond.FacetCut[] _facetCuts, address _initAddress);
ethereum/contracts/zksync/interfaces/IDiamondCut.sol:24:    event DiamondCutProposalExecution(Diamond.DiamondCutData _diamondCut);
ethereum/contracts/zksync/interfaces/IDiamondCut.sol:26:    event EmergencyFreeze();
ethereum/contracts/zksync/interfaces/IDiamondCut.sol:28:    event Unfreeze(uint256 lastDiamondFreezeTimestamp);
ethereum/contracts/zksync/interfaces/IDiamondCut.sol:30:    event EmergencyDiamondCutApproved(
ethereum/contracts/zksync/interfaces/IExecutor.sol:85:    event BlocksRevert(uint256 totalBlocksCommitted, uint256 totalBlocksVerified, uint256 totalBlocksExecuted);
```

## open TODOs
```solidity
ethereum/contracts/zksync/facets/Mailbox.sol:94:        // TODO: estimate gas for L1 execute
ethereum/contracts/zksync/facets/Mailbox.sol:127:        // TODO: Restore after stable priority op fee modeling. (SMA-1230)
ethereum/contracts/zksync/facets/Mailbox.sol:169:                layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)
ethereum/contracts/zksync/Config.sol:28:// TODO: change constant to the real root hash of empty Merkle tree (SMA-184)
ethereum/contracts/zksync/interfaces/IExecutor.sol:56:    /// TODO: The verifier integration is not finished yet, change the structure for compatibility later
```




# Non Critical


## `require()`/`revert()` statements should have descriptive reason strings
```solidity

ethereum/contracts/bridge/L1EthBridge.sol:145:        require(amount != 0);
ethereum/contracts/bridge/L1EthBridge.sol:221:        require(_message.length == 56);
ethereum/contracts/bridge/L1EthBridge.sol:224:        require(bytes4(functionSignature) == this.finalizeWithdrawal.selector);
ethereum/contracts/bridge/L1EthBridge.sol:238:        require(callSuccess);  
ethereum/contracts/zksync/facets/Executor.sol:297:        require(_recurisiveAggregationInput.length == 4);
zksync/contracts/bridge/L2StandardERC20.sol:96:        require(msg.sender == l2Bridge);
zksync/contracts/bridge/L2ETHBridge.sol:50:        require(_l1Token == CONVENTIONAL_ETH_ADDRESS);
```
##  Inconsistent function return patterns affects readability
**Summary**: Some functions explicitly return a value while others assign a value to a named return variable.
**Details**: If many functions explicitly return a value, one may incorrectly infer that functions assigning a value to a named return variable are not actually returning anything.
**Mitigation**: Consider refactoring functions to standardise on one return pattern, preferably explicit returns.
```solidity
ethereum/contracts/bridge/L1ERC20Bridge.sol:164:    function _getERC20Getters(address _token) internal view returns (bytes memory data) {
ethereum/contracts/bridge/L1ERC20Bridge.sol:282:    function l2TokenAddress(address _l1Token) public view returns (address) {
ethereum/contracts/bridge/L1EthBridge.sol:243:    function l2TokenAddress(address) public pure returns (address) {
ethereum/contracts/bridge/interfaces/IL1Bridge.sol:15:    function isWithdrawalFinalized(uint256 _l2BlockNumber, uint256 _l2MessageIndex) external view returns (bool);
ethereum/contracts/bridge/interfaces/IL1Bridge.sol:41:    function l2TokenAddress(address _l1Token) external view returns (address);
ethereum/contracts/bridge/interfaces/IL2Bridge.sol:21:    function l1TokenAddress(address _l2Token) external view returns (address);
ethereum/contracts/bridge/interfaces/IL2Bridge.sol:23:    function l2TokenAddress(address _l1Token) external view returns (address);
ethereum/contracts/bridge/interfaces/IL2Bridge.sol:25:    function l1Bridge() external view returns (address);
ethereum/contracts/common/libraries/UncheckedMath.sol:6:    function uncheckedInc(uint256 _number) internal pure returns (uint256) {
ethereum/contracts/common/libraries/UncheckedMath.sol:12:    function uncheckedAdd(uint256 _lhs, uint256 _rhs) internal pure returns (uint256) {
ethereum/contracts/common/libraries/UnsafeBytes.sol:8: * @dev Each of the functions accepts the `bytes memory` and the offset where data should be read and returns a value of a certain type.
ethereum/contracts/common/libraries/UnsafeBytes.sol:18:    function readUint32(bytes memory _bytes, uint256 _start) internal pure returns (uint32 result, uint256 offset) {
ethereum/contracts/common/libraries/UnsafeBytes.sol:25:    function readAddress(bytes memory _bytes, uint256 _start) internal pure returns (address result, uint256 offset) {
ethereum/contracts/common/libraries/UnsafeBytes.sol:32:    function readUint256(bytes memory _bytes, uint256 _start) internal pure returns (uint256 result, uint256 offset) {
ethereum/contracts/common/libraries/UnsafeBytes.sol:39:    function readBytes32(bytes memory _bytes, uint256 _start) internal pure returns (bytes32 result, uint256 offset) {
ethereum/contracts/common/L2ContractHelper.sol:6:    function sendToL1(bytes memory _message) external returns (bytes32);
ethereum/contracts/common/L2ContractHelper.sol:43:    function sendMessageToL1(bytes memory _message) internal returns (bytes32) {
ethereum/contracts/common/L2ContractHelper.sol:47:    function hashL2Bytecode(bytes memory _bytecode) internal pure returns (bytes32 hashedBytecode) {
ethereum/contracts/common/L2ContractHelper.sol:71:    function bytecodeLen(bytes32 _bytecodeHash) internal pure returns (uint256 codeLengthInWords) {
ethereum/contracts/common/interfaces/IAllowList.sol:27:    function pendingOwner() external view returns (address);
ethereum/contracts/common/interfaces/IAllowList.sol:29:    function owner() external view returns (address);
ethereum/contracts/common/interfaces/IAllowList.sol:31:    function isAccessPublic(address _target) external view returns (bool);
ethereum/contracts/zksync/libraries/PriorityQueue.sol:34:    function getFirstUnprocessedPriorityTx(Queue storage _queue) internal view returns (uint256) {
ethereum/contracts/zksync/libraries/PriorityQueue.sol:39:    function getTotalPriorityTxs(Queue storage _queue) internal view returns (uint256) {
ethereum/contracts/zksync/libraries/PriorityQueue.sol:44:    function getSize(Queue storage _queue) internal view returns (uint256) {
ethereum/contracts/zksync/libraries/PriorityQueue.sol:49:    function isEmpty(Queue storage _queue) internal view returns (bool) {
ethereum/contracts/zksync/libraries/PriorityQueue.sol:63:    function front(Queue storage _queue) internal view returns (PriorityOperation memory) {
ethereum/contracts/zksync/libraries/PriorityQueue.sol:71:    function popFront(Queue storage _queue) internal returns (PriorityOperation memory priorityOperation) {
ethereum/contracts/zksync/libraries/Diamond.sol:80:    function getDiamondStorage() internal pure returns (DiamondStorage storage diamondStorage) {
ethereum/contracts/zksync/facets/Getters.sol:22:    function getVerifier() external view returns (address) {
ethereum/contracts/zksync/facets/Getters.sol:27:    function getGovernor() external view returns (address) {
ethereum/contracts/zksync/facets/Getters.sol:32:    function getPendingGovernor() external view returns (address) {
ethereum/contracts/zksync/facets/Getters.sol:37:    function getTotalBlocksCommitted() external view returns (uint256) {
ethereum/contracts/zksync/facets/Getters.sol:42:    function getTotalBlocksVerified() external view returns (uint256) {
ethereum/contracts/zksync/facets/Getters.sol:47:    function getTotalBlocksExecuted() external view returns (uint256) {
ethereum/contracts/zksync/facets/Getters.sol:52:    function getTotalPriorityTxs() external view returns (uint256) {
ethereum/contracts/zksync/facets/Getters.sol:59:    function getFirstUnprocessedPriorityTx() external view returns (uint256) {
ethereum/contracts/zksync/facets/Getters.sol:64:    function getPriorityQueueSize() external view returns (uint256) {
ethereum/contracts/zksync/facets/Getters.sol:69:    function priorityQueueFrontOperation() external view returns (PriorityOperation memory) {
ethereum/contracts/zksync/facets/Getters.sol:74:    function isValidator(address _address) external view returns (bool) {
ethereum/contracts/zksync/facets/Getters.sol:79:    function l2LogsRootHash(uint256 _blockNumber) external view returns (bytes32) {
ethereum/contracts/zksync/facets/Getters.sol:86:    function storedBlockHash(uint256 _blockNumber) external view returns (bytes32) {
ethereum/contracts/zksync/facets/Getters.sol:91:    function getProposedDiamondCutHash() external view returns (bytes32) {
ethereum/contracts/zksync/facets/Getters.sol:96:    function getProposedDiamondCutTimestamp() external view returns (uint256) {
ethereum/contracts/zksync/facets/Getters.sol:101:    function getLastDiamondFreezeTimestamp() external view returns (uint256) {
ethereum/contracts/zksync/facets/Getters.sol:106:    function getCurrentProposalId() external view returns (uint256) {
ethereum/contracts/zksync/facets/Getters.sol:111:    function getSecurityCouncilEmergencyApprovals() external view returns (uint256) {
ethereum/contracts/zksync/facets/Getters.sol:116:    function isSecurityCouncilMember(address _address) external view returns (bool) {
ethereum/contracts/zksync/facets/Getters.sol:122:    function getSecurityCouncilMemberLastApprovedProposalId(address _address) external view returns (uint256) {
ethereum/contracts/zksync/facets/Getters.sol:127:    function isDiamondStorageFrozen() external view returns (bool) {
ethereum/contracts/zksync/facets/Getters.sol:133:    function isFacetFreezable(address _facet) external view returns (bool isFreezable) {
ethereum/contracts/zksync/facets/Getters.sol:146:    function isFunctionFreezable(bytes4 _selector) external view returns (bool) {
ethereum/contracts/zksync/facets/Getters.sol:157:    function facets() external view returns (Facet[] memory result) {
ethereum/contracts/zksync/facets/Getters.sol:172:    function facetFunctionSelectors(address _facet) external view returns (bytes4[] memory) {
ethereum/contracts/zksync/facets/Getters.sol:178:    function facetAddresses() external view returns (address[] memory) {
ethereum/contracts/zksync/facets/Getters.sol:184:    function facetAddress(bytes4 _selector) external view returns (address) {
ethereum/contracts/zksync/facets/Executor.sol:177:    function _collectOperationsFromPriorityQueue(uint256 _nPriorityOps) internal returns (bytes32 concatHash) {
ethereum/contracts/zksync/facets/Executor.sol:296:    function _verifyRecursivePartOfProof(uint256[] calldata _recurisiveAggregationInput) internal view returns (bool) {
ethereum/contracts/zksync/facets/Executor.sol:349:    function _maxU256(uint256 a, uint256 b) internal pure returns (uint256) {
ethereum/contracts/zksync/facets/Executor.sol:354:    function _createBlockCommitment(CommitBlockInfo calldata _newBlockData) internal view returns (bytes32) {
ethereum/contracts/zksync/facets/Executor.sol:362:    function _blockPassThroughData(CommitBlockInfo calldata _block) internal pure returns (bytes memory) {
ethereum/contracts/zksync/facets/Executor.sol:372:    function _blockMetaParameters() internal view returns (bytes memory) {
ethereum/contracts/zksync/facets/Executor.sol:376:    function _blockAuxilaryOutput(CommitBlockInfo calldata _block) internal pure returns (bytes memory) {
ethereum/contracts/zksync/facets/Executor.sol:385:    function _hashStoredBlockInfo(StoredBlockInfo memory _storedBlockInfo) internal pure returns (bytes32) {
ethereum/contracts/zksync/facets/Mailbox.sol:75:    function _L2MessageToLog(L2Message calldata _message) internal pure returns (L2Log memory) {
ethereum/contracts/zksync/facets/DiamondCut.sol:126:    function _resetProposal() private returns (bool) {
ethereum/contracts/zksync/interfaces/IGetters.sol:12:    function getVerifier() external view returns (address);
ethereum/contracts/zksync/interfaces/IGetters.sol:14:    function getGovernor() external view returns (address);
ethereum/contracts/zksync/interfaces/IGetters.sol:16:    function getPendingGovernor() external view returns (address);
ethereum/contracts/zksync/interfaces/IGetters.sol:18:    function getTotalBlocksCommitted() external view returns (uint256);
ethereum/contracts/zksync/interfaces/IGetters.sol:20:    function getTotalBlocksVerified() external view returns (uint256);
ethereum/contracts/zksync/interfaces/IGetters.sol:22:    function getTotalBlocksExecuted() external view returns (uint256);
ethereum/contracts/zksync/interfaces/IGetters.sol:24:    function getTotalPriorityTxs() external view returns (uint256);
ethereum/contracts/zksync/interfaces/IGetters.sol:26:    function getFirstUnprocessedPriorityTx() external view returns (uint256);
ethereum/contracts/zksync/interfaces/IGetters.sol:28:    function getPriorityQueueSize() external view returns (uint256);
ethereum/contracts/zksync/interfaces/IGetters.sol:30:    function priorityQueueFrontOperation() external view returns (PriorityOperation memory);
ethereum/contracts/zksync/interfaces/IGetters.sol:32:    function isValidator(address _address) external view returns (bool);
ethereum/contracts/zksync/interfaces/IGetters.sol:34:    function l2LogsRootHash(uint256 _blockNumber) external view returns (bytes32 hash);
ethereum/contracts/zksync/interfaces/IGetters.sol:36:    function storedBlockHash(uint256 _blockNumber) external view returns (bytes32);
ethereum/contracts/zksync/interfaces/IGetters.sol:38:    function isDiamondStorageFrozen() external view returns (bool);
ethereum/contracts/zksync/interfaces/IGetters.sol:40:    function getProposedDiamondCutHash() external view returns (bytes32);
ethereum/contracts/zksync/interfaces/IGetters.sol:42:    function getProposedDiamondCutTimestamp() external view returns (uint256);
ethereum/contracts/zksync/interfaces/IGetters.sol:44:    function getLastDiamondFreezeTimestamp() external view returns (uint256);
ethereum/contracts/zksync/interfaces/IGetters.sol:46:    function getCurrentProposalId() external view returns (uint256);
ethereum/contracts/zksync/interfaces/IGetters.sol:48:    function getSecurityCouncilEmergencyApprovals() external view returns (uint256);
ethereum/contracts/zksync/interfaces/IGetters.sol:50:    function isSecurityCouncilMember(address _address) external view returns (bool);
ethereum/contracts/zksync/interfaces/IGetters.sol:52:    function getSecurityCouncilMemberLastApprovedProposalId(address _address) external view returns (uint256);
ethereum/contracts/zksync/interfaces/IGetters.sol:66:    function facets() external view returns (Facet[] memory);
ethereum/contracts/zksync/interfaces/IGetters.sol:68:    function facetFunctionSelectors(address _facet) external view returns (bytes4[] memory);
ethereum/contracts/zksync/interfaces/IGetters.sol:70:    function facetAddresses() external view returns (address[] memory facets);
ethereum/contracts/zksync/interfaces/IGetters.sol:72:    function facetAddress(bytes4 _selector) external view returns (address facet);
ethereum/contracts/zksync/interfaces/IGetters.sol:74:    function isFunctionFreezable(bytes4 _selector) external view returns (bool);
zksync/contracts/ExternalDecoder.sol:10:    function decodeString(bytes memory _input) external pure returns (string memory result) {
zksync/contracts/ExternalDecoder.sol:15:    function decodeUint8(bytes memory _input) external pure returns (uint8 result) {
zksync/contracts/L2ContractHelper.sol:6:    function sendToL1(bytes memory _message) external returns (bytes32);
zksync/contracts/L2ContractHelper.sol:26:    function sendMessageToL1(bytes memory _message) internal returns (bytes32) {
zksync/contracts/bridge/L2StandardERC20.sol:114:    function name() public view override returns (string memory) {
zksync/contracts/bridge/L2StandardERC20.sol:120:    function symbol() public view override returns (string memory) {
zksync/contracts/bridge/L2StandardERC20.sol:126:    function decimals() public view override returns (uint8) {
zksync/contracts/bridge/L2ERC20Bridge.sol:73:    function _deployL2Token(address _l1Token, bytes calldata _data) internal returns (address) {
zksync/contracts/bridge/L2ERC20Bridge.sol:113:    function l2TokenAddress(address _l1Token) public view override returns (address) {
zksync/contracts/bridge/L2ERC20Bridge.sol:122:    function _getCreate2Salt(address _l1Token) internal pure returns (bytes32 salt) {
zksync/contracts/bridge/L2ETHBridge.sol:74:    function _getL1WithdrawMessage(address _to, uint256 _amount) internal pure returns (bytes memory) {
zksync/contracts/bridge/L2ETHBridge.sol:79:    function l2TokenAddress(address) public pure returns (address) {
zksync/contracts/bridge/L2ETHBridge.sol:84:    function l1TokenAddress(address) public pure override returns (address) {
zksync/contracts/bridge/interfaces/IL2StandardToken.sol:14:    function l1Address() external view returns (address);
zksync/contracts/bridge/interfaces/IL2StandardToken.sol:16:    function l2Bridge() external view returns (address);
zksync/contracts/bridge/interfaces/IL2Bridge.sol:35:    function l1TokenAddress(address _l2Token) external view returns (address);
zksync/contracts/bridge/interfaces/IL2Bridge.sol:37:    function l2TokenAddress(address _l1Token) external view returns (address);
zksync/contracts/bridge/interfaces/IL2Bridge.sol:39:    function l1Bridge() external view returns (address);

```

