## [G-01] Don't Initialize Variables with Default Value

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```
2022-10-zksync/ethereum/contracts/common/AllowList.sol::69 => for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {
2022-10-zksync/ethereum/contracts/common/AllowList.sol::103 => for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::111 => for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::162 => for (uint256 i = 0; i < blocksLength; i = i.uncheckedInc()) {
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::180 => for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::210 => for (uint256 i = 0; i < nBlocks; i = i.uncheckedInc()) {
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::240 => for (uint256 i = 0; i < committedBlocksLength; i = i.uncheckedInc()) {
2022-10-zksync/ethereum/contracts/zksync/facets/Getters.sol::163 => for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::223 => for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::94 => for (uint256 i = 0; i < facetCutsLength; ++i) {
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::132 => for (uint256 i = 0; i < selectorsLength; ++i) {
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::153 => for (uint256 i = 0; i < selectorsLength; ++i) {
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::173 => for (uint256 i = 0; i < selectorsLength; ++i) {
2022-10-zksync/ethereum/contracts/zksync/libraries/Merkle.sol::28 => for (uint256 i = 0; i < pathLength; i = i.uncheckedInc()) {
```

## [G-02] Cache Array Length Outside of Loop

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

```
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::111 => for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
```

## [G-03] Using > 0 costs more gas than != 0 when used on a uint in a require() statement

When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0. This change saves 6 gas per instance

```
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::117 => require(amount > 0, "1T"); // empty deposit amount
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::210 => require(amount > 0, "y1");
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::100 => require(selectors.length > 0, "B"); // no functions for diamond cut
2022-10-zksync/ethereum/contracts/zksync/libraries/Merkle.sol::23 => require(pathLength > 0, "xc");
```

## [G-04] Use Shift Right/Left instead of Division/Multiplication if possible

A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

```
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::26 => uint160 constant SYSTEM_CONTRACTS_OFFSET = 0x8000; // 2^15
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::72 => codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
2022-10-zksync/ethereum/contracts/zksync/Config.sol::23 => uint256 constant INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + 64 * 4896;
2022-10-zksync/zksync/contracts/L2ContractHelper.sol::17 => uint160 constant SYSTEM_CONTRACTS_OFFSET = 0x8000; // 2^15
```

## [G-05] Use calldata instead of memory

Use calldata instead of memory for function parameters saves gas if the function argument is only read.

```
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::6 => function sendToL1(bytes memory _message) external returns (bytes32);
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::43 => function sendMessageToL1(bytes memory _message) internal returns (bytes32) {
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::47 => function hashL2Bytecode(bytes memory _bytecode) internal pure returns (bytes32 hashedBytecode) {
2022-10-zksync/ethereum/contracts/common/libraries/UnsafeBytes.sol::8 => * @dev Each of the functions accepts the `bytes memory` and the offset where data should be read and returns a value of a certain type.
2022-10-zksync/ethereum/contracts/common/libraries/UnsafeBytes.sol::18 => function readUint32(bytes memory _bytes, uint256 _start) internal pure returns (uint32 result, uint256 offset) {
2022-10-zksync/ethereum/contracts/common/libraries/UnsafeBytes.sol::25 => function readAddress(bytes memory _bytes, uint256 _start) internal pure returns (address result, uint256 offset) {
2022-10-zksync/ethereum/contracts/common/libraries/UnsafeBytes.sol::32 => function readUint256(bytes memory _bytes, uint256 _start) internal pure returns (uint256 result, uint256 offset) {
2022-10-zksync/ethereum/contracts/common/libraries/UnsafeBytes.sol::39 => function readBytes32(bytes memory _bytes, uint256 _start) internal pure returns (bytes32 result, uint256 offset) {
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::385 => function _hashStoredBlockInfo(StoredBlockInfo memory _storedBlockInfo) internal pure returns (bytes32) {
2022-10-zksync/zksync/contracts/ExternalDecoder.sol::10 => function decodeString(bytes memory _input) external pure returns (string memory result) {
2022-10-zksync/zksync/contracts/ExternalDecoder.sol::15 => function decodeUint8(bytes memory _input) external pure returns (uint8 result) {
2022-10-zksync/zksync/contracts/L2ContractHelper.sol::6 => function sendToL1(bytes memory _message) external returns (bytes32);
2022-10-zksync/zksync/contracts/L2ContractHelper.sol::26 => function sendMessageToL1(bytes memory _message) internal returns (bytes32) {
```

## [G-06] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

```
2022-10-zksync/ethereum/contracts/common/AllowList.sol::57 => function setPublicAccess(address _target, bool _enable) external onlyOwner {
2022-10-zksync/ethereum/contracts/common/AllowList.sol::65 => function setBatchPublicAccess(address[] calldata _targets, bool[] calldata _enables) external onlyOwner {
2022-10-zksync/ethereum/contracts/common/AllowList.sol::140 => function setPendingOwner(address _newPendingOwner) external onlyOwner {
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::22 => function proposeDiamondCut(Diamond.FacetCut[] calldata _facetCuts, address _initAddress) external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::35 => function cancelDiamondCutProposal() external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::46 => function executeDiamondCutProposal(Diamond.DiamondCutData calldata _diamondCut) external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::78 => function emergencyFreezeDiamond() external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::91 => function unfreezeDiamond() external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::208 => function executeBlocks(StoredBlockInfo[] calldata _blocksData) external nonReentrant onlyValidator {
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::336 => function revertBlocks(uint256 _newLastBlock) external nonReentrant onlyValidator {
2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol::15 => function setPendingGovernor(address _newPendingGovernor) external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol::45 => function setValidator(address _validator, bool _active) external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol::54 => function setL2BootloaderBytecodeHash(bytes32 _l2BootloaderBytecodeHash) external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol::69 => function setL2DefaultAccountBytecodeHash(bytes32 _l2DefaultAccountBytecodeHash) external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol::84 => function setPorterAvailability(bool _zkPorterIsAvailable) external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol::94 => function setVerifier(Verifier _newVerifier) external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol::104 => function setVerifierParams(VerifierParams calldata _newVerifierParams) external onlyGovernor {
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol::102 => function bridgeMint(address _to, uint256 _amount) external override onlyBridge {
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol::109 => function bridgeBurn(address _from, uint256 _amount) external override onlyBridge {
```

## [G-07] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. 

```
2022-10-zksync/ethereum/contracts/zksync/DiamondInit.sol::15 => constructor() reentrancyGuardInitializer {}
2022-10-zksync/ethereum/contracts/zksync/interfaces/IZkSync.sol::11 => interface IZkSync is IMailbox, IGovernance, IExecutor, IDiamondCut, IGetters {}
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol::39 => constructor() initializer {}
```

## [G-08] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

```
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::26 => uint160 constant SYSTEM_CONTRACTS_OFFSET = 0x8000; // 2^15
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::64 => uint8 version = uint8(_bytecodeHash[0]);
2022-10-zksync/ethereum/contracts/zksync/Storage.sol::40 => uint8 l2ShardId;
2022-10-zksync/ethereum/contracts/zksync/Storage.sol::42 => uint16 txNumberInBlock;
2022-10-zksync/ethereum/contracts/zksync/Storage.sol::54 => uint16 txNumberInBlock;
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::125 => uint64 expirationBlock = uint64(block.number + PRIORITY_EXPIRATION);
2022-10-zksync/ethereum/contracts/zksync/interfaces/IExecutor.sol::16 => uint64 blockNumber;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IExecutor.sol::18 => uint64 indexRepeatedStorageChanges;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IExecutor.sol::41 => uint64 blockNumber;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IExecutor.sol::42 => uint64 timestamp;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IExecutor.sol::43 => uint64 indexRepeatedStorageChanges;
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::24 => uint16 selectorPosition;
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::33 => uint16 facetPosition;
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::207 => uint16 selectorPosition = uint16(ds.facetToSelectors[_facet].selectors.length);
2022-10-zksync/ethereum/contracts/zksync/libraries/PriorityQueue.sol::12 => uint64 expirationBlock;
2022-10-zksync/zksync/contracts/L2ContractHelper.sol::17 => uint160 constant SYSTEM_CONTRACTS_OFFSET = 0x8000; // 2^15
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol::29 => uint8 private decimals_;
```

## [G-09] Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
Use uint256(1) and uint256(2) for true/false instead

```
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::40 => mapping(uint256 => mapping(uint256 => bool)) public isWithdrawalFinalized;
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::37 => mapping(uint256 => mapping(uint256 => bool)) public isWithdrawalFinalized;
2022-10-zksync/ethereum/contracts/common/AllowList.sol::26 => mapping(address => bool) public isAccessPublic;
2022-10-zksync/ethereum/contracts/common/AllowList.sol::30 => mapping(address => mapping(address => mapping(bytes4 => bool))) public hasSpecialAccessToCall;
```

## [G-10] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, for example when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

```
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::94 => for (uint256 i = 0; i < facetCutsLength; ++i) {
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::132 => for (uint256 i = 0; i < selectorsLength; ++i) {
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::153 => for (uint256 i = 0; i < selectorsLength; ++i) {
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::173 => for (uint256 i = 0; i < selectorsLength; ++i) {
```

## [G-11] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

use <x> = <x> + <y> or <x> = <x> - <y> instead to save gas

```
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::29 => s.diamondCutStorage.currentProposalId += 1;
```

## [G-12] abi.encode() is less efficient than abi.encodePacked()

use abi.encodePacked() where possible to save gas

```
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::82 => bytes memory create2Input = abi.encode(address(this), l2ProxyTokenBytecodeHash, _governor);
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::168 => data = abi.encode(data1, data2, data3);
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::283 => bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::58 => bytes memory create2Input = abi.encode(address(this));
2022-10-zksync/ethereum/contracts/zksync/DiamondInit.sol::54 => s.storedBlockHashes[0] = keccak256(abi.encode(storedBlockZero));
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::27 => s.diamondCutStorage.proposedDiamondCutHash = keccak256(abi.encode(_facetCuts, _initAddress));
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::61 => keccak256(abi.encode(_diamondCut.facetCuts, _diamondCut.initAddress)),
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::85 => return keccak256(abi.encode(_previousBlock.blockHash, _newBlock.newStateRoot));
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::122 => chainedPriorityTxsHash = keccak256(abi.encode(chainedPriorityTxsHash, canonicalTxHash));
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::182 => concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::359 => return keccak256(abi.encode(passThroughDataHash, metadataHash, auxiliaryOutputHash));
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::381 => return abi.encode(_block.l2LogsTreeRoot, l2ToL1LogsHash, initialStorageChangesHash, repeatedStorageChangesHash);
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::386 => return keccak256(abi.encode(_storedBlockInfo));
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::163 => canonicalTxHash = keccak256(abi.encode(transaction));
2022-10-zksync/ethereum/contracts/zksync/libraries/Merkle.sol::30 => currentHash = keccak256(abi.encode(currentHash, _path[i]));
2022-10-zksync/ethereum/contracts/zksync/libraries/Merkle.sol::32 => currentHash = keccak256(abi.encode(_path[i], currentHash));
2022-10-zksync/zksync/contracts/bridge/L2ERC20Bridge.sol::114 => bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));
```

## [G-13] Use custom errors rather than revert()/require() strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

```
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::77 => require(_factoryDeps.length == 2, "mk");
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::117 => require(amount > 0, "1T"); // empty deposit amount
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::207 => require(success, "yn");
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::210 => require(amount > 0, "y1");
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::232 => require(!isWithdrawalFinalized[_l2BlockNumber][_l2MessageIndex], "pw");
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::249 => require(success, "nq");
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::271 => require(_l2ToL1message.length == 76, "kk");
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::274 => require(bytes4(functionSignature) == this.finalizeWithdrawal.selector, "nt");
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::93 => require(_l1Token == CONVENTIONAL_ETH_ADDRESS, "bx");
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::141 => require(_l1Token == CONVENTIONAL_ETH_ADDRESS, "sj");
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::166 => require(success, "ju");
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::189 => require(!isWithdrawalFinalized[_l2BlockNumber][_l2MessageIndex], "jj");
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::205 => require(success, "rj");
2022-10-zksync/ethereum/contracts/common/AllowList.sol::33 => require(_owner != address(0), "kq");
2022-10-zksync/ethereum/contracts/common/AllowList.sol::38 => require(msg.sender == owner, "kx");
2022-10-zksync/ethereum/contracts/common/AllowList.sol::67 => require(targetsLength == _enables.length, "yg"); // The size of arrays should be equal
2022-10-zksync/ethereum/contracts/common/AllowList.sol::155 => require(msg.sender == newOwner, "n0"); // Only proposed by current owner address can claim the owner rights
2022-10-zksync/ethereum/contracts/common/AllowListed.sol::15 => require(_allowList.canCall(msg.sender, address(this), functionSig), "nr");
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::50 => require(_bytecode.length % 32 == 0, "po");
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::53 => require(bytecodeLenInWords < 2**16, "pp"); // bytecode length must be less than 2^16 words
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::54 => require(bytecodeLenInWords % 2 == 1, "pr"); // bytecode length in words must be odd
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::65 => require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::67 => require(bytecodeLen(_bytecodeHash) % 2 == 1, "uy"); // Code length in words must be odd
2022-10-zksync/ethereum/contracts/common/ReentrancyGuard.sol::55 => require(lockSlotOldValue == 0, "1B");
2022-10-zksync/ethereum/contracts/common/ReentrancyGuard.sol::72 => require(_status == _NOT_ENTERED, "r1");
2022-10-zksync/ethereum/contracts/zksync/DiamondProxy.sol::13 => require(_chainId == block.chainid, "pr");
2022-10-zksync/ethereum/contracts/zksync/DiamondProxy.sol::24 => require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
2022-10-zksync/ethereum/contracts/zksync/DiamondProxy.sol::29 => require(facetAddress != address(0), "F"); // Proxy has no facet for this selector
2022-10-zksync/ethereum/contracts/zksync/DiamondProxy.sol::30 => require(!diamondStorage.isFrozen || !facet.isFreezable, "q1"); // Facet is frozen
2022-10-zksync/ethereum/contracts/zksync/facets/Base.sol::16 => require(msg.sender == s.governor, "1g"); // only by governor
2022-10-zksync/ethereum/contracts/zksync/facets/Base.sol::22 => require(s.validators[msg.sender], "1h"); // validator is not active
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::23 => require(s.diamondCutStorage.proposedDiamondCutTimestamp == 0, "a3"); // proposal already exists
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::40 => require(_resetProposal(), "g1"); // failed cancel diamond cut
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::55 => require(approvedBySecurityCouncil || upgradeNoticePeriodPassed, "a6"); // notice period should expire
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::56 => require(approvedBySecurityCouncil || !diamondStorage.isFrozen, "f3");
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::65 => require(_resetProposal(), "a5"); // failed reset proposal
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::80 => require(!diamondStorage.isFrozen, "a9"); // diamond proxy is frozen already
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::94 => require(diamondStorage.isFrozen, "a7"); // diamond proxy is not frozen
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::106 => require(s.diamondCutStorage.securityCouncilMembers[msg.sender], "a9"); // not a security council member
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::108 => require(s.diamondCutStorage.securityCouncilMemberLastApprovedProposalId[msg.sender] < currentProposalId, "ao"); // already approved this proposal
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::111 => require(s.diamondCutStorage.proposedDiamondCutTimestamp != 0, "f0"); // there is no proposed diamond cut
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::112 => require(s.diamondCutStorage.proposedDiamondCutHash == _diamondCutHash, "f1"); // proposed diamond cut do not match to the approved
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::28 => require(_newBlock.blockNumber == _previousBlock.blockNumber + 1, "f"); // only commit next block
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::39 => require(_previousBlock.blockHash == previousBlockHash, "l");
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::41 => require(expectedPriorityOperationsHash == _newBlock.priorityOperationsHash, "t");
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::52 => require(timestampNotTooSmall, "h"); // New block timestamp is too small
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::53 => require(timestampNotTooBig, "h1"); // New block timestamp is too big
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::117 => require(keccak256(l2Messages[currentMessage]) == hashedMessage, "k2");
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::129 => require(!isSystemContextLogProcessed, "fx");
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::136 => require(bytecodeHash == L2ContractHelper.hashL2Bytecode(factoryDeps[currentBytecode]), "k3");
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::142 => require(currentBytecode == factoryDeps.length, "ym");
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::143 => require(currentMessage == l2Messages.length, "pl");
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::145 => require(isSystemContextLogProcessed, "by");
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::159 => require(s.storedBlockHashes[s.totalBlocksCommitted] == _hashStoredBlockInfo(_lastCommittedBlockData), "i"); // incorrect previous block data
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::192 => require(currentBlockNumber == s.totalBlocksExecuted + _executedBlockIdx + 1, "k"); // Execute blocks in order
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::199 => require(priorityOperationsHash == _storedBlock.priorityOperationsHash, "x"); // priority operations hash does not match to expected
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::216 => require(s.totalBlocksExecuted <= s.totalBlocksVerified, "n"); // Can't execute blocks more then committed and proven currently.
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::237 => require(_hashStoredBlockInfo(_prevBlock) == s.storedBlockHashes[currentTotalBlocksVerified], "t1");
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::261 => require(successVerifyProof, "p"); // Proof verification fail
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::265 => require(successProofAggregation, "hh"); // Proof aggregation must be valid
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::268 => require(currentTotalBlocksVerified <= s.totalBlocksCommitted, "q");
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::337 => require(s.totalBlocksCommitted > _newLastBlock, "v1"); // the last committed block is less new last block
2022-10-zksync/ethereum/contracts/zksync/facets/Getters.sol::148 => require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");
2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol::30 => require(msg.sender == pendingGovernor, "n4"); // Only proposed by current governor address can claim the governor rights
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::56 => require(_blockNumber <= s.totalBlocksExecuted, "xx");
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::63 => require(hashedLog != L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH, "tw");
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::66 => require(_proof.length == L2_TO_L1_LOG_MERKLE_TREE_HEIGHT, "rz");
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::124 => require(_ergsLimit <= PRIORITY_TX_MAX_ERGS_LIMIT, "ui");
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::100 => require(selectors.length > 0, "B"); // no functions for diamond cut
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::126 => require(_facet != address(0), "G"); // facet with zero address cannot be added
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::135 => require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::150 => require(_facet != address(0), "K"); // cannot replace facet with zero address
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::156 => require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::170 => require(_facet == address(0), "a1"); // facet address must be zero
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::176 => require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::214 => require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::279 => require(_calldata.length == 0, "H"); // Non-empty calldata for zero address
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::283 => require(success, "I"); // delegatecall failed
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::287 => require(data.length == 32, "lp");
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::288 => require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");
2022-10-zksync/ethereum/contracts/zksync/libraries/Merkle.sol::23 => require(pathLength > 0, "xc");
2022-10-zksync/ethereum/contracts/zksync/libraries/Merkle.sol::24 => require(pathLength < 256, "bt");
2022-10-zksync/ethereum/contracts/zksync/libraries/Merkle.sol::25 => require(_index < 2**pathLength, "pz");
2022-10-zksync/ethereum/contracts/zksync/libraries/PriorityQueue.sol::64 => require(!_queue.isEmpty(), "D"); // priority queue is empty
2022-10-zksync/ethereum/contracts/zksync/libraries/PriorityQueue.sol::72 => require(!_queue.isEmpty(), "s"); // priority queue is empty
2022-10-zksync/zksync/contracts/bridge/L2ERC20Bridge.sol::58 => require(msg.sender == l1Bridge, "mq");
2022-10-zksync/zksync/contracts/bridge/L2ERC20Bridge.sol::63 => require(deployedToken == expectedL2Token, "mt");
2022-10-zksync/zksync/contracts/bridge/L2ERC20Bridge.sol::95 => require(l1Token != address(0), "yh");
2022-10-zksync/zksync/contracts/bridge/L2ETHBridge.sol::49 => require(msg.sender == l1Bridge, "ni");
2022-10-zksync/zksync/contracts/bridge/L2ETHBridge.sol::64 => require(_l2Token == CONVENTIONAL_ETH_ADDRESS, "zn");
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol::44 => require(l1Address == address(0), "in5"); // Is already initialized
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol::45 => require(_l1Address != address(0), "in6"); // Should be non-zero address
```

## [G-14] Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.
Consequences: each usage of a constant costs more gas on each access. Since these are not real constants, they can't be referenced from a real constant environment (e.g. from assembly, or from another library)

```
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::28 => address constant BOOTLOADER_ADDRESS = address(SYSTEM_CONTRACTS_OFFSET + 0x01);
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::30 => address constant DEPLOYER_SYSTEM_CONTRACT_ADDRESS = address(SYSTEM_CONTRACTS_OFFSET + 0x06);
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::34 => address constant FORCE_DEPLOYER = address(SYSTEM_CONTRACTS_OFFSET + 0x07);
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::36 => IL2Messenger constant L2_MESSENGER = IL2Messenger(address(SYSTEM_CONTRACTS_OFFSET + 0x08));
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::38 => address constant VALUE_SIMULATOR_SYSTEM_CONTRACT_ADDRESS = address(SYSTEM_CONTRACTS_OFFSET + 0x09);
2022-10-zksync/ethereum/contracts/zksync/Config.sol::12 => uint256 constant L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;
2022-10-zksync/ethereum/contracts/zksync/Config.sol::23 => uint256 constant INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + 64 * 4896;
2022-10-zksync/ethereum/contracts/zksync/Config.sol::26 => uint256 constant REPEATED_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + 40 * 7787;
2022-10-zksync/zksync/contracts/L2ContractHelper.sol::19 => address constant BOOTLOADER_ADDRESS = address(SYSTEM_CONTRACTS_OFFSET + 0x01);
2022-10-zksync/zksync/contracts/L2ContractHelper.sol::20 => address constant VALUE_SIMULATOR_SYSTEM_CONTRACT_ADDRESS = address(SYSTEM_CONTRACTS_OFFSET + 0x09);
2022-10-zksync/zksync/contracts/L2ContractHelper.sol::21 => IL2Messenger constant L2_MESSENGER = IL2Messenger(address(SYSTEM_CONTRACTS_OFFSET + 0x08));
```

## [G-15] Use a more recent version of solidity

Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath
Use a solidity version of at least 0.8.2 to get compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

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

## [G-16] Splitting require() statements that use && saves gas

Saves 16 gas per instance.
If you're using the Optimizer at 200, instead of using the && operator in a single require statement to check multiple conditions, multiple require statements with 1 condition per require statement should be used to save gas:

```
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::65 => require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
```

## [G-17] Usage of assert() instead of require()

Between solc 0.4.10 and 0.8.0, require() used REVERT (0xfd) opcode which refunded remaining gas on failure while assert() used INVALID (0xfe) opcode which consumed all the supplied gas.
require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking (properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix).

```
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::16 => assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);
```

## [G-18] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public and can save gas by doing so.

```
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::243 => function l2TokenAddress(address) public pure returns (address) {
2022-10-zksync/zksync/contracts/bridge/L2ETHBridge.sol::79 => function l2TokenAddress(address) public pure returns (address) {
2022-10-zksync/zksync/contracts/bridge/L2ETHBridge.sol::84 => function l1TokenAddress(address) public pure override returns (address) {
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol::126 => function decimals() public view override returns (uint8) {
```

## [G-19] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

```
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::44 => mapping(address => mapping(address => mapping(bytes32 => uint256))) depositAmount;
2022-10-zksync/ethereum/contracts/common/AllowList.sol::26 => mapping(address => bool) public isAccessPublic;
2022-10-zksync/ethereum/contracts/common/AllowList.sol::30 => mapping(address => mapping(address => mapping(bytes4 => bool))) public hasSpecialAccessToCall;
2022-10-zksync/ethereum/contracts/zksync/Storage.sol::22 => mapping(address => bool) securityCouncilMembers;
2022-10-zksync/ethereum/contracts/zksync/Storage.sol::23 => mapping(address => uint256) securityCouncilMemberLastApprovedProposalId;
2022-10-zksync/ethereum/contracts/zksync/Storage.sol::77 => mapping(address => bool) validators;
```

## [G-20] Use assembly to check for address(0)

Saves 6 gas per instance if using assembly to check for address(0)

e.g.
```
assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}
```

instances:

```
2022-10-zksync/ethereum/contracts/common/AllowList.sol::33 => require(_owner != address(0), "kq");
2022-10-zksync/ethereum/contracts/zksync/DiamondProxy.sol::29 => require(facetAddress != address(0), "F"); // Proxy has no facet for this selector
2022-10-zksync/ethereum/contracts/zksync/facets/Getters.sol::148 => require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::126 => require(_facet != address(0), "G"); // facet with zero address cannot be added
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::150 => require(_facet != address(0), "K"); // cannot replace facet with zero address
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::156 => require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::176 => require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet
2022-10-zksync/zksync/contracts/bridge/L2ERC20Bridge.sol::95 => require(l1Token != address(0), "yh");
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol::45 => require(_l1Address != address(0), "in6"); // Should be non-zero address
```

## [G-21] Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read.

Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

```
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::71 => bytes[] memory factoryDeps = new bytes[](1);
2022-10-zksync/ethereum/contracts/zksync/DiamondProxy.sol::26 => Diamond.SelectorToFacet memory facet = diamondStorage.selectorToFacet[msg.sig];
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::100 => bytes memory emittedL2Logs = _newBlock.l2Logs[4:];
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::234 => uint256[] memory proofPublicInput = new uint256[](committedBlocksLength);
2022-10-zksync/ethereum/contracts/zksync/facets/Getters.sol::165 => Diamond.FacetToSelectors memory facetToSelectors = ds.facetToSelectors[facetAddr];
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::98 => bytes4[] memory selectors = facetCuts[i].selectors;
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::134 => SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::155 => SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::175 => SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
```

## [G-22] internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::164 => function _getERC20Getters(address _token) internal view returns (bytes memory data) {
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::43 => function sendMessageToL1(bytes memory _message) internal returns (bytes32) {
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::47 => function hashL2Bytecode(bytes memory _bytecode) internal pure returns (bytes32 hashedBytecode) {
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::63 => function validateBytecodeHash(bytes32 _bytecodeHash) internal pure {
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::71 => function bytecodeLen(bytes32 _bytecodeHash) internal pure returns (uint256 codeLengthInWords) {
2022-10-zksync/ethereum/contracts/common/libraries/UncheckedMath.sol::6 => function uncheckedInc(uint256 _number) internal pure returns (uint256) {
2022-10-zksync/ethereum/contracts/common/libraries/UncheckedMath.sol::12 => function uncheckedAdd(uint256 _lhs, uint256 _rhs) internal pure returns (uint256) {
2022-10-zksync/ethereum/contracts/common/libraries/UnsafeBytes.sol::18 => function readUint32(bytes memory _bytes, uint256 _start) internal pure returns (uint32 result, uint256 offset) {
2022-10-zksync/ethereum/contracts/common/libraries/UnsafeBytes.sol::25 => function readAddress(bytes memory _bytes, uint256 _start) internal pure returns (address result, uint256 offset) {
2022-10-zksync/ethereum/contracts/common/libraries/UnsafeBytes.sol::32 => function readUint256(bytes memory _bytes, uint256 _start) internal pure returns (uint256 result, uint256 offset) {
2022-10-zksync/ethereum/contracts/common/libraries/UnsafeBytes.sol::39 => function readBytes32(bytes memory _bytes, uint256 _start) internal pure returns (bytes32 result, uint256 offset) {
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::177 => function _collectOperationsFromPriorityQueue(uint256 _nPriorityOps) internal returns (bytes32 concatHash) {
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::190 => function _executeOneBlock(StoredBlockInfo memory _storedBlock, uint256 _executedBlockIdx) internal {
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::296 => function _verifyRecursivePartOfProof(uint256[] calldata _recurisiveAggregationInput) internal view returns (bool) {
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::349 => function _maxU256(uint256 a, uint256 b) internal pure returns (uint256) {
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::354 => function _createBlockCommitment(CommitBlockInfo calldata _newBlockData) internal view returns (bytes32) {
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::362 => function _blockPassThroughData(CommitBlockInfo calldata _block) internal pure returns (bytes memory) {
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::372 => function _blockMetaParameters() internal view returns (bytes memory) {
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::376 => function _blockAuxilaryOutput(CommitBlockInfo calldata _block) internal pure returns (bytes memory) {
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::75 => function _L2MessageToLog(L2Message calldata _message) internal pure returns (L2Log memory) {
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::89 => function diamondCut(DiamondCutData memory _diamondCut) internal {
2022-10-zksync/ethereum/contracts/zksync/libraries/PriorityQueue.sol::34 => function getFirstUnprocessedPriorityTx(Queue storage _queue) internal view returns (uint256) {
2022-10-zksync/ethereum/contracts/zksync/libraries/PriorityQueue.sol::39 => function getTotalPriorityTxs(Queue storage _queue) internal view returns (uint256) {
2022-10-zksync/ethereum/contracts/zksync/libraries/PriorityQueue.sol::44 => function getSize(Queue storage _queue) internal view returns (uint256) {
2022-10-zksync/ethereum/contracts/zksync/libraries/PriorityQueue.sol::54 => function pushBack(Queue storage _queue, PriorityOperation memory _operation) internal {
2022-10-zksync/ethereum/contracts/zksync/libraries/PriorityQueue.sol::63 => function front(Queue storage _queue) internal view returns (PriorityOperation memory) {
2022-10-zksync/ethereum/contracts/zksync/libraries/PriorityQueue.sol::71 => function popFront(Queue storage _queue) internal returns (PriorityOperation memory priorityOperation) {
2022-10-zksync/zksync/contracts/L2ContractHelper.sol::26 => function sendMessageToL1(bytes memory _message) internal returns (bytes32) {
2022-10-zksync/zksync/contracts/bridge/L2ERC20Bridge.sol::73 => function _deployL2Token(address _l1Token, bytes calldata _data) internal returns (address) {
2022-10-zksync/zksync/contracts/bridge/L2ETHBridge.sol::74 => function _getL1WithdrawMessage(address _to, uint256 _amount) internal pure returns (bytes memory) {
```

## [G-23] internal functions not called by the contract should be removed to save deployment gas

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

```
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::43 => function sendMessageToL1(bytes memory _message) internal returns (bytes32) {
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::47 => function hashL2Bytecode(bytes memory _bytecode) internal pure returns (bytes32 hashedBytecode) {
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::63 => function validateBytecodeHash(bytes32 _bytecodeHash) internal pure {
2022-10-zksync/zksync/contracts/L2ContractHelper.sol::26 => function sendMessageToL1(bytes memory _message) internal returns (bytes32) {
```