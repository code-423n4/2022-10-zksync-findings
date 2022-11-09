# zkSync v2 Contest Gas Optimization Report

## Summary

The following gas optimization issues were found during the code audit:

1. Use `calldata` instead of `memory` (83 instances)
2. Cache `<array>.length` (20 instances)
3. Use `unchecked{}` to suppress overflow/underflow check (4 instances)
4. Using `bool`s for storage incurs overhead (37 instances)
5. Use `!= 0` instead of `> 0` when comparing uint (5 instances)
6. Empty blocks should be removed or emit something (4 instances)
7. Don't initialize variables with default value (38 instances)
8. Split `require(xxx && yyy)` to `require(xxx)` and `require(yyy)` (1 instance)
9. Use `abi.encodePacked()` instead of `abi.encode()` (17 instances)
10. Don't compare boolean expressions to boolean literals (1 instance)
11. Use custom errors instead of `revert()`/`require()` strings (96 instances)
12. Use shift right/left instead of division/multiplication if possible (2 instances)

Total 308 instances of 12 issues.

## 1. Use `calldata` instead of `memory` (83 instances)

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for loop to copy each index of the `calldata` to the `memory` index. This overhead can be optimized by using `calldata` directly.

```solidity
ethereum/contracts/bridge/L1ERC20Bridge.sol::164 => function _getERC20Getters(address _token) internal view returns (bytes memory data) {

ethereum/contracts/bridge/L1ERC20Bridge.sol::260 => function _parseL2WithdrawalMessage(bytes memory _l2ToL1message)

ethereum/contracts/bridge/L1EthBridge.sol::214 => function _parseL2WithdrawalMessage(bytes memory _message)

ethereum/contracts/common/L2ContractHelper.sol::6 => function sendToL1(bytes memory _message) external returns (bytes32);

ethereum/contracts/common/L2ContractHelper.sol::43 => function sendMessageToL1(bytes memory _message) internal returns (bytes32) {

ethereum/contracts/common/L2ContractHelper.sol::47 => function hashL2Bytecode(bytes memory _bytecode) internal pure returns (bytes32 hashedBytecode) {

ethereum/contracts/common/libraries/UnsafeBytes.sol::18 => function readUint32(bytes memory _bytes, uint256 _start) internal pure returns (uint32 result, uint256 offset) {

ethereum/contracts/common/libraries/UnsafeBytes.sol::25 => function readAddress(bytes memory _bytes, uint256 _start) internal pure returns (address result, uint256 offset) {

ethereum/contracts/common/libraries/UnsafeBytes.sol::32 => function readUint256(bytes memory _bytes, uint256 _start) internal pure returns (uint256 result, uint256 offset) {

ethereum/contracts/common/libraries/UnsafeBytes.sol::39 => function readBytes32(bytes memory _bytes, uint256 _start) internal pure returns (bytes32 result, uint256 offset) {

ethereum/contracts/dev-contracts/test/DiamondCutTest.sol::9 => function diamondCut(Diamond.DiamondCutData memory _diamondCut) external {

ethereum/contracts/dev-contracts/test/DummyERC20BytesTransferReturnValue.sol::12 => function transfer(address _recipient, uint256 _amount) external view returns (bytes memory) {

ethereum/contracts/dev-contracts/test/PriorityQueueTest.sol::28 => function pushBack(PriorityOperation memory _operation) external {

ethereum/contracts/dev-contracts/test/PriorityQueueTest.sol::32 => function front() external view returns (PriorityOperation memory) {

ethereum/contracts/dev-contracts/test/PriorityQueueTest.sol::36 => function popFront() external returns (PriorityOperation memory operation) {

ethereum/contracts/dev-contracts/test/UnsafeBytesTest.sol::10 => function readUint32(bytes memory _bytes, uint256 _start) external pure returns (uint32 readValue, uint256 offset) {

ethereum/contracts/dev-contracts/test/UnsafeBytesTest.sol::14 => function readAddress(bytes memory _bytes, uint256 _start)

ethereum/contracts/dev-contracts/test/UnsafeBytesTest.sol::22 => function readUint256(bytes memory _bytes, uint256 _start)

ethereum/contracts/dev-contracts/test/UnsafeBytesTest.sol::30 => function readBytes32(bytes memory _bytes, uint256 _start)

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::85 => function evaluate_l0_at_point(uint256 domain_size, PairingsBn254.Fr memory at)

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::128 => function evaluate_vanishing(uint256 domain_size, PairingsBn254.Fr memory at)

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::137 => function initialize_transcript(Proof memory proof, VerificationKey memory vk)

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::207 => function compute_powers_of_alpha(PartialVerifierState memory state) public pure {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::218 => function verify(Proof memory proof, VerificationKey memory vk) internal view returns (bool) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::530 => function lookup_linearization_contribution(Proof memory proof, PartialVerifierState memory state)

ethereum/contracts/zksync/Verifier.sol::11 => function get_verification_key() internal pure returns (VerificationKey memory vk) {

ethereum/contracts/zksync/facets/Executor.sol::23 => function _commitOneBlock(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)

ethereum/contracts/zksync/facets/Executor.sol::80 => function _calculateBlockHash(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)

ethereum/contracts/zksync/facets/Executor.sol::152 => function commitBlocks(StoredBlockInfo memory _lastCommittedBlockData, CommitBlockInfo[] calldata _newBlocksData)

ethereum/contracts/zksync/facets/Executor.sol::190 => function _executeOneBlock(StoredBlockInfo memory _storedBlock, uint256 _executedBlockIdx) internal {

ethereum/contracts/zksync/facets/Executor.sol::362 => function _blockPassThroughData(CommitBlockInfo calldata _block) internal pure returns (bytes memory) {

ethereum/contracts/zksync/facets/Executor.sol::372 => function _blockMetaParameters() internal view returns (bytes memory) {

ethereum/contracts/zksync/facets/Executor.sol::376 => function _blockAuxilaryOutput(CommitBlockInfo calldata _block) internal pure returns (bytes memory) {

ethereum/contracts/zksync/facets/Executor.sol::385 => function _hashStoredBlockInfo(StoredBlockInfo memory _storedBlockInfo) internal pure returns (bytes32) {

ethereum/contracts/zksync/facets/Getters.sol::69 => function priorityQueueFrontOperation() external view returns (PriorityOperation memory) {

ethereum/contracts/zksync/facets/Getters.sol::157 => function facets() external view returns (Facet[] memory result) {

ethereum/contracts/zksync/facets/Getters.sol::172 => function facetFunctionSelectors(address _facet) external view returns (bytes4[] memory) {

ethereum/contracts/zksync/facets/Getters.sol::178 => function facetAddresses() external view returns (address[] memory) {

ethereum/contracts/zksync/facets/Mailbox.sol::75 => function _L2MessageToLog(L2Message calldata _message) internal pure returns (L2Log memory) {

ethereum/contracts/zksync/interfaces/IGetters.sol::30 => function priorityQueueFrontOperation() external view returns (PriorityOperation memory);

ethereum/contracts/zksync/interfaces/IGetters.sol::66 => function facets() external view returns (Facet[] memory);

ethereum/contracts/zksync/interfaces/IGetters.sol::68 => function facetFunctionSelectors(address _facet) external view returns (bytes4[] memory);

ethereum/contracts/zksync/interfaces/IGetters.sol::70 => function facetAddresses() external view returns (address[] memory facets);

ethereum/contracts/zksync/libraries/Diamond.sol::89 => function diamondCut(DiamondCutData memory _diamondCut) internal {

ethereum/contracts/zksync/libraries/Diamond.sol::167 => function _removeFunctions(address _facet, bytes4[] memory _selectors) private {

ethereum/contracts/zksync/libraries/Diamond.sol::277 => function _initializeDiamondCut(address _init, bytes memory _calldata) private {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::19 => function new_fr(uint256 fr) internal pure returns (Fr memory) {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::24 => function copy(Fr memory self) internal pure returns (Fr memory n) {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::28 => function assign(Fr memory self, Fr memory other) internal pure {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::32 => function inverse(Fr memory fr) internal view returns (Fr memory) {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::37 => function add_assign(Fr memory self, Fr memory other) internal pure {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::41 => function sub_assign(Fr memory self, Fr memory other) internal pure {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::45 => function mul_assign(Fr memory self, Fr memory other) internal pure {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::49 => function pow(Fr memory self, uint256 power) internal view returns (Fr memory) {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::66 => function P1() internal pure returns (G1Point memory) {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::70 => function new_g1(uint256 x, uint256 y) internal pure returns (G1Point memory) {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::75 => function new_g1_checked(uint256 x, uint256 y) internal pure returns (G1Point memory) {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::95 => function new_g2(uint256[2] memory x, uint256[2] memory y) internal pure returns (G2Point memory) {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::99 => function copy_g1(G1Point memory self) internal pure returns (G1Point memory result) {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::104 => function P2() internal pure returns (G2Point memory) {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::120 => function negate(G1Point memory self) internal pure {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::130 => function point_add(G1Point memory p1, G1Point memory p2) internal view returns (G1Point memory r) {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::135 => function point_add_assign(G1Point memory p1, G1Point memory p2) internal view {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::170 => function point_sub_assign(G1Point memory p1, G1Point memory p2) internal view {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::205 => function point_mul(G1Point memory p, Fr memory s) internal view returns (G1Point memory r) {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::216 => function point_mul_assign(G1Point memory p, Fr memory s) internal view {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::236 => function pairing(G1Point[] memory p1, G2Point[] memory p2) internal view returns (bool) {

ethereum/contracts/zksync/libraries/PriorityQueue.sol::54 => function pushBack(Queue storage _queue, PriorityOperation memory _operation) internal {

ethereum/contracts/zksync/libraries/PriorityQueue.sol::63 => function front(Queue storage _queue) internal view returns (PriorityOperation memory) {

ethereum/contracts/zksync/libraries/PriorityQueue.sol::71 => function popFront(Queue storage _queue) internal returns (PriorityOperation memory priorityOperation) {

ethereum/contracts/zksync/libraries/TranscriptLib.sol::21 => function new_transcript() internal pure returns (Transcript memory t) {

ethereum/contracts/zksync/libraries/TranscriptLib.sol::27 => function update_with_u256(Transcript memory self, uint256 value) internal pure {

ethereum/contracts/zksync/libraries/TranscriptLib.sol::33 => function update_with_fr(Transcript memory self, PairingsBn254.Fr memory value) internal pure {

ethereum/contracts/zksync/libraries/TranscriptLib.sol::37 => function update_with_g1(Transcript memory self, PairingsBn254.G1Point memory p) internal pure {

ethereum/contracts/zksync/libraries/TranscriptLib.sol::42 => function get_challenge(Transcript memory self) internal pure returns (PairingsBn254.Fr memory challenge) {

zksync/contracts/bridge/L2ETHBridge.sol::74 => function _getL1WithdrawMessage(address _to, uint256 _amount) internal pure returns (bytes memory) {

zksync/contracts/bridge/L2StandardERC20.sol::43 => function bridgeInitialize(address _l1Address, bytes memory _data) external initializer {

zksync/contracts/bridge/L2StandardERC20.sol::114 => function name() public view override returns (string memory) {

zksync/contracts/bridge/L2StandardERC20.sol::120 => function symbol() public view override returns (string memory) {

zksync/contracts/ExternalDecoder.sol::10 => function decodeString(bytes memory _input) external pure returns (string memory result) {

zksync/contracts/ExternalDecoder.sol::15 => function decodeUint8(bytes memory _input) external pure returns (uint8 result) {

zksync/contracts/L2ContractHelper.sol::6 => function sendToL1(bytes memory _message) external returns (bytes32);

zksync/contracts/L2ContractHelper.sol::26 => function sendMessageToL1(bytes memory _message) internal returns (bytes32) {
```

## 2. Cache `<array>.length` (20 instances)

If `<array>.length` is used as for loop termination condition, then the `.length` method will be called in each iteration. Caching it in a local variable can save gas.

```solidity
ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::164 => for (uint256 i = 0; i < proof.quotient_poly_parts_commitments.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::171 => for (uint256 i = 0; i < proof.state_polys_openings_at_z.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::175 => for (uint256 i = 0; i < proof.state_polys_openings_at_z_omega.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::178 => for (uint256 i = 0; i < proof.gate_selectors_openings_at_z.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::181 => for (uint256 i = 0; i < proof.copy_permutation_polys_openings_at_z.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::212 => for (uint256 i = 2; i < state.alpha_values.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::233 => for (uint256 i = 1; i < proof.quotient_poly_parts_commitments.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::255 => for (uint256 i = 1; i < queries.commitments_at_z.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::272 => for (uint256 i = 1; i < queries.commitments_at_z_omega.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::301 => for (uint256 i = 0; i < lagrange_poly_numbers.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::325 => for (uint256 i = 0; i < proof.copy_permutation_polys_openings_at_z.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::449 => for (uint256 i = 0; i < proof.state_polys_openings_at_z.length; ) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::638 => for (uint256 i = 1; i < vk.lookup_tables_commitments.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Verifier.sol::134 => for (uint256 i = 0; i < public_inputs.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Verifier.sol::164 => for (uint256 i = 0; i < proof.quotient_poly_parts_commitments.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Verifier.sol::172 => for (uint256 i = 0; i < proof.state_polys_openings_at_z.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Verifier.sol::178 => for (uint256 i = 0; i < proof.state_polys_openings_at_z_omega.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Verifier.sol::183 => for (uint256 i = 0; i < proof.gate_selectors_openings_at_z.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Verifier.sol::188 => for (uint256 i = 0; i < proof.copy_permutation_polys_openings_at_z.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/facets/Executor.sol::111 => for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
```

## 3. Use `unchecked{}` to suppress overflow/underflow check (4 instances)

Starting from version 0.8.0, Solidity does overflow/underflow checks by default. It is a good feature to prevent vulnerabilities but it has a significant overhead, especially when used in for loop. When using uint256/int256, it is extremely hard to trigger overflow, so it makes sense to skip these checks. To suppress the overflow/underflow checks, use `unchecked {}`. For increment situations, just use `unchecked {}` directly; for decrement situations, add a `require()` statement before decrementing to prevent underflow.

```solidity
ethereum/contracts/zksync/libraries/Diamond.sol::94 => for (uint256 i = 0; i < facetCutsLength; ++i) {

ethereum/contracts/zksync/libraries/Diamond.sol::132 => for (uint256 i = 0; i < selectorsLength; ++i) {

ethereum/contracts/zksync/libraries/Diamond.sol::153 => for (uint256 i = 0; i < selectorsLength; ++i) {

ethereum/contracts/zksync/libraries/Diamond.sol::173 => for (uint256 i = 0; i < selectorsLength; ++i) {
```

## 4. Using `bool`s for storage incurs overhead (37 instances)

Use `uint256(1)` and `uint256(2)` for true/false. Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

```solidity
ethereum/contracts/bridge/L1ERC20Bridge.sol::206 => bool success = zkSyncMailbox.proveL2LogInclusion(_l2BlockNumber, _l2MessageIndex, l2Log, _merkleProof);

ethereum/contracts/bridge/L1ERC20Bridge.sol::243 => bool success = zkSyncMailbox.proveL2MessageInclusion(

ethereum/contracts/bridge/L1EthBridge.sol::165 => bool success = zkSyncMailbox.proveL2LogInclusion(_l2BlockNumber, _l2MessageIndex, l2Log, _merkleProof);

ethereum/contracts/bridge/L1EthBridge.sol::199 => bool success = zkSyncMailbox.proveL2MessageInclusion(

ethereum/contracts/bridge/L1EthBridge.sol::233 => bool callSuccess;

ethereum/contracts/common/AllowList.sol::76 => bool isEnabled = isAccessPublic[_target];

ethereum/contracts/common/AllowList.sol::93 => bool[] calldata _enables

ethereum/contracts/common/AllowList.sol::117 => bool _enable

ethereum/contracts/common/AllowList.sol::127 => bool _enable

ethereum/contracts/common/AllowList.sol::129 => bool currentPermission = hasSpecialAccessToCall[_caller][_target][_functionSig];

ethereum/contracts/common/interfaces/IAllowList.sol::57 => bool[] calldata _enables

ethereum/contracts/common/interfaces/IAllowList.sol::64 => bool _enable

ethereum/contracts/zksync/DiamondInit.sol::34 => bool _zkPorterIsAvailable,

ethereum/contracts/zksync/Storage.sol::41 => bool isService;

ethereum/contracts/zksync/Storage.sol::104 => bool zkPorterIsAvailable;

ethereum/contracts/zksync/facets/DiamondCut.sol::49 => bool approvedBySecurityCouncil = s.diamondCutStorage.securityCouncilEmergencyApprovals >=

ethereum/contracts/zksync/facets/DiamondCut.sol::52 => bool upgradeNoticePeriodPassed = block.timestamp >=

ethereum/contracts/zksync/facets/Executor.sol::50 => bool timestampNotTooSmall = block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= l2BlockTimestamp;

ethereum/contracts/zksync/facets/Executor.sol::51 => bool timestampNotTooBig = l2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA;

ethereum/contracts/zksync/facets/Executor.sol::104 => bool isSystemContextLogProcessed;

ethereum/contracts/zksync/facets/Executor.sol::260 => bool successVerifyProof = s.verifier.verify_serialized_proof(proofPublicInput, _proof.serializedProof);

ethereum/contracts/zksync/facets/Executor.sol::264 => bool successProofAggregation = _verifyRecursivePartOfProof(_proof.recurisiveAggregationInput);

ethereum/contracts/zksync/libraries/Diamond.sol::25 => bool isFreezable;

ethereum/contracts/zksync/libraries/Diamond.sol::46 => bool isFrozen;

ethereum/contracts/zksync/libraries/Diamond.sol::57 => bool isFreezable;

ethereum/contracts/zksync/libraries/Diamond.sol::97 => bool isFacetFreezable = facetCuts[i].isFreezable;

ethereum/contracts/zksync/libraries/Diamond.sol::122 => bool _isFacetFreezable

ethereum/contracts/zksync/libraries/Diamond.sol::146 => bool _isFacetFreezable

ethereum/contracts/zksync/libraries/Diamond.sol::203 => bool _isSelectorFreezable

ethereum/contracts/zksync/libraries/PairingsBn254.sol::52 => bool success;

ethereum/contracts/zksync/libraries/PairingsBn254.sol::162 => bool success;

ethereum/contracts/zksync/libraries/PairingsBn254.sol::197 => bool success = false;

ethereum/contracts/zksync/libraries/PairingsBn254.sol::229 => bool success;

ethereum/contracts/zksync/libraries/PairingsBn254.sol::253 => bool success;

zksync/contracts/bridge/L2StandardERC20.sol::19 => bool ignoreName;

zksync/contracts/bridge/L2StandardERC20.sol::20 => bool ignoreSymbol;

zksync/contracts/bridge/L2StandardERC20.sol::21 => bool ignoreDecimals;
```

## 5. Use `!= 0` instead of `> 0` when comparing uint (5 instances)

When dealing with unsigned integer types, comparisons with `!= 0` are cheaper then with `> 0`.

```solidity
ethereum/contracts/bridge/L1ERC20Bridge.sol::117 => require(amount > 0, "1T"); // empty deposit amount

ethereum/contracts/bridge/L1ERC20Bridge.sol::210 => require(amount > 0, "y1");

ethereum/contracts/zksync/facets/DiamondCut.sol::16 => assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);

ethereum/contracts/zksync/libraries/Diamond.sol::100 => require(selectors.length > 0, "B"); // no functions for diamond cut

ethereum/contracts/zksync/libraries/Merkle.sol::23 => require(pathLength > 0, "xc");
```

## 6. Empty blocks should be removed or emit something (4 instances)

Empty blocks exist in the code. The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

```solidity
ethereum/contracts/dev-contracts/test/DummyERC20NoTransferReturnValue.sol::6 => function transfer(address recipient, uint256 amount) external {}

ethereum/contracts/zksync/DiamondInit.sol::15 => constructor() reentrancyGuardInitializer {}

ethereum/contracts/zksync/interfaces/IZkSync.sol::11 => interface IZkSync is IMailbox, IGovernance, IExecutor, IDiamondCut, IGetters {}

zksync/contracts/bridge/L2StandardERC20.sol::39 => constructor() initializer {}
```

## 7. Don't initialize variables with default value (38 instances)

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```solidity
ethereum/contracts/common/AllowList.sol::69 => for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {

ethereum/contracts/common/AllowList.sol::103 => for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::144 => for (uint256 i = 0; i < vk.num_inputs; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::148 => for (uint256 i = 0; i < STATE_WIDTH; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::164 => for (uint256 i = 0; i < proof.quotient_poly_parts_commitments.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::171 => for (uint256 i = 0; i < proof.state_polys_openings_at_z.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::175 => for (uint256 i = 0; i < proof.state_polys_openings_at_z_omega.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::178 => for (uint256 i = 0; i < proof.gate_selectors_openings_at_z.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::181 => for (uint256 i = 0; i < proof.copy_permutation_polys_openings_at_z.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::301 => for (uint256 i = 0; i < lagrange_poly_numbers.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::307 => for (uint256 i = 0; i < vk.num_inputs; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::325 => for (uint256 i = 0; i < proof.copy_permutation_polys_openings_at_z.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::449 => for (uint256 i = 0; i < proof.state_polys_openings_at_z.length; ) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::473 => for (uint256 i = 0; i < STATE_WIDTH - 1; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::560 => for (uint256 i = 0; i < STATE_WIDTH - 1; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::615 => for (uint256 i = 0; i < STATE_WIDTH; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::624 => for (uint256 i = 0; i < STATE_WIDTH - 1; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Verifier.sol::134 => for (uint256 i = 0; i < public_inputs.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Verifier.sol::139 => for (uint256 i = 0; i < STATE_WIDTH; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Verifier.sol::164 => for (uint256 i = 0; i < proof.quotient_poly_parts_commitments.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Verifier.sol::172 => for (uint256 i = 0; i < proof.state_polys_openings_at_z.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Verifier.sol::178 => for (uint256 i = 0; i < proof.state_polys_openings_at_z_omega.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Verifier.sol::183 => for (uint256 i = 0; i < proof.gate_selectors_openings_at_z.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/Verifier.sol::188 => for (uint256 i = 0; i < proof.copy_permutation_polys_openings_at_z.length; i = i.uncheckedInc()) {

ethereum/contracts/zksync/facets/Executor.sol::111 => for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {

ethereum/contracts/zksync/facets/Executor.sol::162 => for (uint256 i = 0; i < blocksLength; i = i.uncheckedInc()) {

ethereum/contracts/zksync/facets/Executor.sol::180 => for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {

ethereum/contracts/zksync/facets/Executor.sol::210 => for (uint256 i = 0; i < nBlocks; i = i.uncheckedInc()) {

ethereum/contracts/zksync/facets/Executor.sol::240 => for (uint256 i = 0; i < committedBlocksLength; i = i.uncheckedInc()) {

ethereum/contracts/zksync/facets/Getters.sol::163 => for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {

ethereum/contracts/zksync/facets/Mailbox.sol::223 => for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {

ethereum/contracts/zksync/libraries/Diamond.sol::94 => for (uint256 i = 0; i < facetCutsLength; ++i) {

ethereum/contracts/zksync/libraries/Diamond.sol::132 => for (uint256 i = 0; i < selectorsLength; ++i) {

ethereum/contracts/zksync/libraries/Diamond.sol::153 => for (uint256 i = 0; i < selectorsLength; ++i) {

ethereum/contracts/zksync/libraries/Diamond.sol::173 => for (uint256 i = 0; i < selectorsLength; ++i) {

ethereum/contracts/zksync/libraries/Merkle.sol::28 => for (uint256 i = 0; i < pathLength; i = i.uncheckedInc()) {

ethereum/contracts/zksync/libraries/PairingsBn254.sol::197 => bool success = false;

ethereum/contracts/zksync/libraries/PairingsBn254.sol::241 => for (uint256 i = 0; i < elements; ) {
```

## 8. Split `require(xxx && yyy)` to `require(xxx)` and `require(yyy)` (1 instance)

Instead of using operator && on single require check, using double `require()` checks can save more gas.

```solidity
ethereum/contracts/common/L2ContractHelper.sol::65 => require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
```

## 9. Use `abi.encodePacked()` instead of `abi.encode()` (17 instances)

`abi.encodePacked()` is more efficient than `abi.encode()`.

```solidity
ethereum/contracts/bridge/L1ERC20Bridge.sol::82 => bytes memory create2Input = abi.encode(address(this), l2ProxyTokenBytecodeHash, _governor);

ethereum/contracts/bridge/L1ERC20Bridge.sol::168 => data = abi.encode(data1, data2, data3);

ethereum/contracts/bridge/L1ERC20Bridge.sol::283 => bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));

ethereum/contracts/bridge/L1EthBridge.sol::58 => bytes memory create2Input = abi.encode(address(this));

ethereum/contracts/zksync/DiamondInit.sol::54 => s.storedBlockHashes[0] = keccak256(abi.encode(storedBlockZero));

ethereum/contracts/zksync/facets/DiamondCut.sol::27 => s.diamondCutStorage.proposedDiamondCutHash = keccak256(abi.encode(_facetCuts, _initAddress));

ethereum/contracts/zksync/facets/DiamondCut.sol::61 => keccak256(abi.encode(_diamondCut.facetCuts, _diamondCut.initAddress)),

ethereum/contracts/zksync/facets/Executor.sol::85 => return keccak256(abi.encode(_previousBlock.blockHash, _newBlock.newStateRoot));

ethereum/contracts/zksync/facets/Executor.sol::122 => chainedPriorityTxsHash = keccak256(abi.encode(chainedPriorityTxsHash, canonicalTxHash));

ethereum/contracts/zksync/facets/Executor.sol::182 => concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));

ethereum/contracts/zksync/facets/Executor.sol::359 => return keccak256(abi.encode(passThroughDataHash, metadataHash, auxiliaryOutputHash));

ethereum/contracts/zksync/facets/Executor.sol::381 => return abi.encode(_block.l2LogsTreeRoot, l2ToL1LogsHash, initialStorageChangesHash, repeatedStorageChangesHash);

ethereum/contracts/zksync/facets/Executor.sol::386 => return keccak256(abi.encode(_storedBlockInfo));

ethereum/contracts/zksync/facets/Mailbox.sol::163 => canonicalTxHash = keccak256(abi.encode(transaction));

ethereum/contracts/zksync/libraries/Merkle.sol::30 => currentHash = keccak256(abi.encode(currentHash, _path[i]));

ethereum/contracts/zksync/libraries/Merkle.sol::32 => currentHash = keccak256(abi.encode(_path[i], currentHash));

zksync/contracts/bridge/L2ERC20Bridge.sol::114 => bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));
```

## 10. Don't compare boolean expressions to boolean literals (1 instance)

`if (<x> == true)` can be refactored to `if (<x>)`, `if (<x> == false)` can be refactored to `if (!<x>)`.

```solidity
ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::221 => if (verify_quotient_evaluation(vk, proof, state) == false) {
```

## 11. Use custom errors instead of `revert()`/`require()` strings (96 instances)

Using `require()`/`revert()` strings is expensive. Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors.

Custom errors decrease both deploy and runtime gas costs. Note that runtime gas cost is only relevant when the revert condition is met.

```solidity
ethereum/contracts/bridge/L1ERC20Bridge.sol::77 => require(_factoryDeps.length == 2, "mk");

ethereum/contracts/bridge/L1ERC20Bridge.sol::117 => require(amount > 0, "1T"); // empty deposit amount

ethereum/contracts/bridge/L1ERC20Bridge.sol::207 => require(success, "yn");

ethereum/contracts/bridge/L1ERC20Bridge.sol::210 => require(amount > 0, "y1");

ethereum/contracts/bridge/L1ERC20Bridge.sol::232 => require(!isWithdrawalFinalized[_l2BlockNumber][_l2MessageIndex], "pw");

ethereum/contracts/bridge/L1ERC20Bridge.sol::249 => require(success, "nq");

ethereum/contracts/bridge/L1ERC20Bridge.sol::271 => require(_l2ToL1message.length == 76, "kk");

ethereum/contracts/bridge/L1ERC20Bridge.sol::274 => require(bytes4(functionSignature) == this.finalizeWithdrawal.selector, "nt");

ethereum/contracts/bridge/L1EthBridge.sol::93 => require(_l1Token == CONVENTIONAL_ETH_ADDRESS, "bx");

ethereum/contracts/bridge/L1EthBridge.sol::141 => require(_l1Token == CONVENTIONAL_ETH_ADDRESS, "sj");

ethereum/contracts/bridge/L1EthBridge.sol::166 => require(success, "ju");

ethereum/contracts/bridge/L1EthBridge.sol::189 => require(!isWithdrawalFinalized[_l2BlockNumber][_l2MessageIndex], "jj");

ethereum/contracts/bridge/L1EthBridge.sol::205 => require(success, "rj");

ethereum/contracts/common/AllowList.sol::33 => require(_owner != address(0), "kq");

ethereum/contracts/common/AllowList.sol::38 => require(msg.sender == owner, "kx");

ethereum/contracts/common/AllowList.sol::67 => require(targetsLength == _enables.length, "yg"); // The size of arrays should be equal

ethereum/contracts/common/AllowList.sol::155 => require(msg.sender == newOwner, "n0"); // Only proposed by current owner address can claim the owner rights

ethereum/contracts/common/AllowListed.sol::15 => require(_allowList.canCall(msg.sender, address(this), functionSig), "nr");

ethereum/contracts/common/L2ContractHelper.sol::50 => require(_bytecode.length % 32 == 0, "po");

ethereum/contracts/common/L2ContractHelper.sol::53 => require(bytecodeLenInWords < 2**16, "pp"); // bytecode length must be less than 2^16 words

ethereum/contracts/common/L2ContractHelper.sol::54 => require(bytecodeLenInWords % 2 == 1, "pr"); // bytecode length in words must be odd

ethereum/contracts/common/L2ContractHelper.sol::65 => require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash

ethereum/contracts/common/L2ContractHelper.sol::67 => require(bytecodeLen(_bytecodeHash) % 2 == 1, "uy"); // Code length in words must be odd

ethereum/contracts/common/ReentrancyGuard.sol::55 => require(lockSlotOldValue == 0, "1B");

ethereum/contracts/common/ReentrancyGuard.sol::72 => require(_status == _NOT_ENTERED, "r1");

ethereum/contracts/zksync/DiamondProxy.sol::13 => require(_chainId == block.chainid, "pr");

ethereum/contracts/zksync/DiamondProxy.sol::24 => require(msg.data.length >= 4 || msg.data.length == 0, "Ut");

ethereum/contracts/zksync/DiamondProxy.sol::29 => require(facetAddress != address(0), "F"); // Proxy has no facet for this selector

ethereum/contracts/zksync/DiamondProxy.sol::30 => require(!diamondStorage.isFrozen || !facet.isFreezable, "q1"); // Facet is frozen

ethereum/contracts/zksync/facets/Base.sol::16 => require(msg.sender == s.governor, "1g"); // only by governor

ethereum/contracts/zksync/facets/Base.sol::22 => require(s.validators[msg.sender], "1h"); // validator is not active

ethereum/contracts/zksync/facets/DiamondCut.sol::23 => require(s.diamondCutStorage.proposedDiamondCutTimestamp == 0, "a3"); // proposal already exists

ethereum/contracts/zksync/facets/DiamondCut.sol::40 => require(_resetProposal(), "g1"); // failed cancel diamond cut

ethereum/contracts/zksync/facets/DiamondCut.sol::55 => require(approvedBySecurityCouncil || upgradeNoticePeriodPassed, "a6"); // notice period should expire

ethereum/contracts/zksync/facets/DiamondCut.sol::56 => require(approvedBySecurityCouncil || !diamondStorage.isFrozen, "f3");

ethereum/contracts/zksync/facets/DiamondCut.sol::65 => require(_resetProposal(), "a5"); // failed reset proposal

ethereum/contracts/zksync/facets/DiamondCut.sol::80 => require(!diamondStorage.isFrozen, "a9"); // diamond proxy is frozen already

ethereum/contracts/zksync/facets/DiamondCut.sol::94 => require(diamondStorage.isFrozen, "a7"); // diamond proxy is not frozen

ethereum/contracts/zksync/facets/DiamondCut.sol::106 => require(s.diamondCutStorage.securityCouncilMembers[msg.sender], "a9"); // not a security council member

ethereum/contracts/zksync/facets/DiamondCut.sol::108 => require(s.diamondCutStorage.securityCouncilMemberLastApprovedProposalId[msg.sender] < currentProposalId, "ao"); // already approved this proposal

ethereum/contracts/zksync/facets/DiamondCut.sol::111 => require(s.diamondCutStorage.proposedDiamondCutTimestamp != 0, "f0"); // there is no proposed diamond cut

ethereum/contracts/zksync/facets/DiamondCut.sol::112 => require(s.diamondCutStorage.proposedDiamondCutHash == _diamondCutHash, "f1"); // proposed diamond cut do not match to the approved

ethereum/contracts/zksync/facets/Executor.sol::28 => require(_newBlock.blockNumber == _previousBlock.blockNumber + 1, "f"); // only commit next block

ethereum/contracts/zksync/facets/Executor.sol::39 => require(_previousBlock.blockHash == previousBlockHash, "l");

ethereum/contracts/zksync/facets/Executor.sol::41 => require(expectedPriorityOperationsHash == _newBlock.priorityOperationsHash, "t");

ethereum/contracts/zksync/facets/Executor.sol::52 => require(timestampNotTooSmall, "h"); // New block timestamp is too small

ethereum/contracts/zksync/facets/Executor.sol::53 => require(timestampNotTooBig, "h1"); // New block timestamp is too big

ethereum/contracts/zksync/facets/Executor.sol::117 => require(keccak256(l2Messages[currentMessage]) == hashedMessage, "k2");

ethereum/contracts/zksync/facets/Executor.sol::129 => require(!isSystemContextLogProcessed, "fx");

ethereum/contracts/zksync/facets/Executor.sol::136 => require(bytecodeHash == L2ContractHelper.hashL2Bytecode(factoryDeps[currentBytecode]), "k3");

ethereum/contracts/zksync/facets/Executor.sol::142 => require(currentBytecode == factoryDeps.length, "ym");

ethereum/contracts/zksync/facets/Executor.sol::143 => require(currentMessage == l2Messages.length, "pl");

ethereum/contracts/zksync/facets/Executor.sol::145 => require(isSystemContextLogProcessed, "by");

ethereum/contracts/zksync/facets/Executor.sol::159 => require(s.storedBlockHashes[s.totalBlocksCommitted] == _hashStoredBlockInfo(_lastCommittedBlockData), "i"); // incorrect previous block data

ethereum/contracts/zksync/facets/Executor.sol::192 => require(currentBlockNumber == s.totalBlocksExecuted + _executedBlockIdx + 1, "k"); // Execute blocks in order

ethereum/contracts/zksync/facets/Executor.sol::199 => require(priorityOperationsHash == _storedBlock.priorityOperationsHash, "x"); // priority operations hash does not match to expected

ethereum/contracts/zksync/facets/Executor.sol::216 => require(s.totalBlocksExecuted <= s.totalBlocksVerified, "n"); // Can't execute blocks more then committed and proven currently.

ethereum/contracts/zksync/facets/Executor.sol::237 => require(_hashStoredBlockInfo(_prevBlock) == s.storedBlockHashes[currentTotalBlocksVerified], "t1");

ethereum/contracts/zksync/facets/Executor.sol::261 => require(successVerifyProof, "p"); // Proof verification fail

ethereum/contracts/zksync/facets/Executor.sol::265 => require(successProofAggregation, "hh"); // Proof aggregation must be valid

ethereum/contracts/zksync/facets/Executor.sol::268 => require(currentTotalBlocksVerified <= s.totalBlocksCommitted, "q");

ethereum/contracts/zksync/facets/Executor.sol::337 => require(s.totalBlocksCommitted > _newLastBlock, "v1"); // the last committed block is less new last block

ethereum/contracts/zksync/facets/Getters.sol::148 => require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");

ethereum/contracts/zksync/facets/Governance.sol::30 => require(msg.sender == pendingGovernor, "n4"); // Only proposed by current governor address can claim the governor rights

ethereum/contracts/zksync/facets/Mailbox.sol::56 => require(_blockNumber <= s.totalBlocksExecuted, "xx");

ethereum/contracts/zksync/facets/Mailbox.sol::63 => require(hashedLog != L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH, "tw");

ethereum/contracts/zksync/facets/Mailbox.sol::66 => require(_proof.length == L2_TO_L1_LOG_MERKLE_TREE_HEIGHT, "rz");

ethereum/contracts/zksync/facets/Mailbox.sol::124 => require(_ergsLimit <= PRIORITY_TX_MAX_ERGS_LIMIT, "ui");

ethereum/contracts/zksync/libraries/Diamond.sol::100 => require(selectors.length > 0, "B"); // no functions for diamond cut

ethereum/contracts/zksync/libraries/Diamond.sol::109 => revert("C"); // undefined diamond cut action

ethereum/contracts/zksync/libraries/Diamond.sol::126 => require(_facet != address(0), "G"); // facet with zero address cannot be added

ethereum/contracts/zksync/libraries/Diamond.sol::135 => require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists

ethereum/contracts/zksync/libraries/Diamond.sol::150 => require(_facet != address(0), "K"); // cannot replace facet with zero address

ethereum/contracts/zksync/libraries/Diamond.sol::156 => require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address

ethereum/contracts/zksync/libraries/Diamond.sol::170 => require(_facet == address(0), "a1"); // facet address must be zero

ethereum/contracts/zksync/libraries/Diamond.sol::176 => require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet

ethereum/contracts/zksync/libraries/Diamond.sol::214 => require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");

ethereum/contracts/zksync/libraries/Diamond.sol::279 => require(_calldata.length == 0, "H"); // Non-empty calldata for zero address

ethereum/contracts/zksync/libraries/Diamond.sol::283 => require(success, "I"); // delegatecall failed

ethereum/contracts/zksync/libraries/Diamond.sol::287 => require(data.length == 32, "lp");

ethereum/contracts/zksync/libraries/Diamond.sol::288 => require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");

ethereum/contracts/zksync/libraries/Merkle.sol::23 => require(pathLength > 0, "xc");

ethereum/contracts/zksync/libraries/Merkle.sol::24 => require(pathLength < 256, "bt");

ethereum/contracts/zksync/libraries/Merkle.sol::25 => require(_index < 2**pathLength, "pz");

ethereum/contracts/zksync/libraries/PairingsBn254.sol::82 => require(x < q_mod, "x axis isn't valid");

ethereum/contracts/zksync/libraries/PairingsBn254.sol::83 => require(y < q_mod, "y axis isn't valid");

ethereum/contracts/zksync/libraries/PairingsBn254.sol::90 => require(lhs == rhs, "is not on curve");

ethereum/contracts/zksync/libraries/PriorityQueue.sol::64 => require(!_queue.isEmpty(), "D"); // priority queue is empty

ethereum/contracts/zksync/libraries/PriorityQueue.sol::72 => require(!_queue.isEmpty(), "s"); // priority queue is empty

zksync/contracts/bridge/L2ERC20Bridge.sol::58 => require(msg.sender == l1Bridge, "mq");

zksync/contracts/bridge/L2ERC20Bridge.sol::63 => require(deployedToken == expectedL2Token, "mt");

zksync/contracts/bridge/L2ERC20Bridge.sol::95 => require(l1Token != address(0), "yh");

zksync/contracts/bridge/L2ETHBridge.sol::49 => require(msg.sender == l1Bridge, "ni");

zksync/contracts/bridge/L2ETHBridge.sol::64 => require(_l2Token == CONVENTIONAL_ETH_ADDRESS, "zn");

zksync/contracts/bridge/L2StandardERC20.sol::44 => require(l1Address == address(0), "in5"); // Is already initialized

zksync/contracts/bridge/L2StandardERC20.sol::45 => require(_l1Address != address(0), "in6"); // Should be non-zero address
```

## 12. Use shift right/left instead of division/multiplication if possible (2 instances)

A division/multiplication by any number `x` being a power of 2 can be calculated by shifting `log2(x)` to the right/left. While the `DIV` opcode uses 5 gas, the `SHR` opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

```solidity
ethereum/contracts/common/L2ContractHelper.sol::72 => codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));

ethereum/contracts/zksync/Config.sol::23 => uint256 constant INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + 64 * 4896;
```
