### Don't Initialize Variables with Default Value

#### Impact
Issue Information: [Uninitialized variables are assigned with the types default value.

Explicitly initializing a variable with it's default value costs unnecessary gas.

Example
🤦 Bad:

uint256 x = 0;
bool y = false;
🚀 Good:

uint256 x;
bool y;

#### Findings:
```
2022-10-zksync/ethereum/contracts/common/AllowList.sol::69 => for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {
2022-10-zksync/ethereum/contracts/common/AllowList.sol::103 => for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
::144 => for (uint256 i = 0; i < vk.num_inputs; i = i.uncheckedInc()) {
::148 => for (uint256 i = 0; i < STATE_WIDTH; i = i.uncheckedInc()) {
::164 => for (uint256 i = 0; i < proof.quotient_poly_parts_commitments.length; i = i.uncheckedInc()) {
::171 => for (uint256 i = 0; i < proof.state_polys_openings_at_z.length; i = i.uncheckedInc()) {
::175 => for (uint256 i = 0; i < proof.state_polys_openings_at_z_omega.length; i = i.uncheckedInc()) {
::178 => for (uint256 i = 0; i < proof.gate_selectors_openings_at_z.length; i = i.uncheckedInc()) {
::181 => for (uint256 i = 0; i < proof.copy_permutation_polys_openings_at_z.length; i = i.uncheckedInc()) {
::301 => for (uint256 i = 0; i < lagrange_poly_numbers.length; i = i.uncheckedInc()) {
::307 => for (uint256 i = 0; i < vk.num_inputs; i = i.uncheckedInc()) {
::325 => for (uint256 i = 0; i < proof.copy_permutation_polys_openings_at_z.length; i = i.uncheckedInc()) {
::449 => for (uint256 i = 0; i < proof.state_polys_openings_at_z.length; ) {
::473 => for (uint256 i = 0; i < STATE_WIDTH - 1; i = i.uncheckedInc()) {
::560 => for (uint256 i = 0; i < STATE_WIDTH - 1; i = i.uncheckedInc()) {
::615 => for (uint256 i = 0; i < STATE_WIDTH; i = i.uncheckedInc()) {
::624 => for (uint256 i = 0; i < STATE_WIDTH - 1; i = i.uncheckedInc()) {


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
::197 => bool success = false;
::241 => for (uint256 i = 0; i < elements; ) {
```
#### Tools used
Manual





================================================================================================


### Cache Array Length Outside of Loop

#### Impact
Issue Information: Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

Example
🤦 Bad:

for (uint256 i = 0; i < array.length; i++) {
    // invariant: array's length is not changed
}
🚀 Good:

uint256 len = array.length
for (uint256 i = 0; i < len; i++) {
    // invariant: array's length is not changed
}

#### Findings:
```
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::77 => require(_factoryDeps.length == 2, "mk");
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::269 => // Check that the message length is correct.
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::270 => // It should be equal to the length of the function signature + address + address + uint256 = 4 + 20 + 20 + 32 = 76 (bytes).
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::271 => require(_l2ToL1message.length == 76, "kk");
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::219 => // Check that the message length is correct.
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::220 => // It should be equal to the length of the function signature + address + uint256 = 4 + 20 + 32 = 56 (bytes).
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::221 => require(_message.length == 56);
2022-10-zksync/ethereum/contracts/common/AllowList.sol::66 => uint256 targetsLength = _targets.length;
2022-10-zksync/ethereum/contracts/common/AllowList.sol::67 => require(targetsLength == _enables.length, "yg"); // The size of arrays should be equal
2022-10-zksync/ethereum/contracts/common/AllowList.sol::95 => uint256 callersLength = _callers.length;
2022-10-zksync/ethereum/contracts/common/AllowList.sol::97 => callersLength == _targets.length &&
2022-10-zksync/ethereum/contracts/common/AllowList.sol::98 => callersLength == _functionSigs.length &&
2022-10-zksync/ethereum/contracts/common/AllowList.sol::99 => callersLength == _enables.length,
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::48 => // Note that the length of the bytecode
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::50 => require(_bytecode.length % 32 == 0, "po");
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::52 => uint256 bytecodeLenInWords = _bytecode.length / 32;
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::53 => require(bytecodeLenInWords < 2**16, "pp"); // bytecode length must be less than 2^16 words
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::54 => require(bytecodeLenInWords % 2 == 1, "pr"); // bytecode length in words must be odd
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::58 => // Setting the length
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::67 => require(bytecodeLen(_bytecodeHash) % 2 == 1, "uy"); // Code length in words must be odd
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::70 => /// @notice returns the length of the bytecode
2022-10-zksync/ethereum/contracts/common/libraries/UnsafeBytes.sol::11 => * 1) Functions don't check the length of the bytes array, so it can go out of bounds.
2022-10-zksync/ethereum/contracts/common/libraries/UnsafeBytes.sol::12 => * The user of the library must check for bytes length before using any functions from the library!
2022-10-zksync/ethereum/contracts/zksync/Config.sol::31 => /// @dev The address of the special smart contract that can send arbitrary length message as an L2 log
2022-10-zksync/ethereum/contracts/zksync/DiamondProxy.sol::23 => // which is not defined for data length in range [1, 3].
2022-10-zksync/ethereum/contracts/zksync/DiamondProxy.sol::24 => require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
2022-10-zksync/ethereum/contracts/zksync/DiamondProxy.sol::41 => // Copy the size length of bytes from return data at zero position to pointer position
2022-10-zksync/ethereum/contracts/zksync/DiamondProxy.sol::50 => // Return data with length of size at pointers position
::164 => for (uint256 i = 0; i < proof.quotient_poly_parts_commitments.length; i = i.uncheckedInc()) {
::171 => for (uint256 i = 0; i < proof.state_polys_openings_at_z.length; i = i.uncheckedInc()) {
::175 => for (uint256 i = 0; i < proof.state_polys_openings_at_z_omega.length; i = i.uncheckedInc()) {
::178 => for (uint256 i = 0; i < proof.gate_selectors_openings_at_z.length; i = i.uncheckedInc()) {
::181 => for (uint256 i = 0; i < proof.copy_permutation_polys_openings_at_z.length; i = i.uncheckedInc()) {
::212 => for (uint256 i = 2; i < state.alpha_values.length; i = i.uncheckedInc()) {
::224 => require(proof.state_polys_openings_at_z_omega.length == 1); // TODO
::233 => for (uint256 i = 1; i < proof.quotient_poly_parts_commitments.length; i = i.uncheckedInc()) {
::248 => require(queries.commitments_at_z.length == queries.values_at_z.length);
::255 => for (uint256 i = 1; i < queries.commitments_at_z.length; i = i.uncheckedInc()) {
::272 => for (uint256 i = 1; i < queries.commitments_at_z_omega.length; i = i.uncheckedInc()) {
::301 => for (uint256 i = 0; i < lagrange_poly_numbers.length; i = i.uncheckedInc()) {
::323 => require(proof.copy_permutation_polys_openings_at_z.length == STATE_WIDTH - 1);
::325 => for (uint256 i = 0; i < proof.copy_permutation_polys_openings_at_z.length; i = i.uncheckedInc()) {
::445 => require(vk.non_residues.length == STATE_WIDTH - 1);
::449 => for (uint256 i = 0; i < proof.state_polys_openings_at_z.length; ) {
::620 => require(proof.gate_selectors_openings_at_z.length == 1);
::638 => for (uint256 i = 1; i < vk.lookup_tables_commitments.length; i = i.uncheckedInc()) {
2022-10-zksync/ethereum/contracts/zksync/Storage.sol::48 => /// @dev An arbitrary length message passed from L2
2022-10-zksync/ethereum/contracts/zksync/Storage.sol::52 => /// @param data An arbitrary length message
::132 => // require(serialized_proof.length == 44); TODO
::133 => proof.input_values = new uint256[](public_inputs.length);
::134 => for (uint256 i = 0; i < public_inputs.length; i = i.uncheckedInc()) {
::164 => for (uint256 i = 0; i < proof.quotient_poly_parts_commitments.length; i = i.uncheckedInc()) {
::172 => for (uint256 i = 0; i < proof.state_polys_openings_at_z.length; i = i.uncheckedInc()) {
::178 => for (uint256 i = 0; i < proof.state_polys_openings_at_z_omega.length; i = i.uncheckedInc()) {
::183 => for (uint256 i = 0; i < proof.gate_selectors_openings_at_z.length; i = i.uncheckedInc()) {
::188 => for (uint256 i = 0; i < proof.copy_permutation_polys_openings_at_z.length; i = i.uncheckedInc()) {
::231 => require(vk.num_inputs == public_inputs.length);
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::111 => for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::142 => require(currentBytecode == factoryDeps.length, "ym");
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::143 => require(currentMessage == l2Messages.length, "pl");
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::161 => uint256 blocksLength = _newBlocksData.length;
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::209 => uint256 nBlocks = _blocksData.length;
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::228 => uint256 committedBlocksLength = _committedBlocks.length;
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::297 => require(_recurisiveAggregationInput.length == 4);
2022-10-zksync/ethereum/contracts/zksync/facets/Getters.sol::138 => uint256 selectorsArrayLen = ds.facetToSelectors[_facet].selectors.length;
2022-10-zksync/ethereum/contracts/zksync/facets/Getters.sol::160 => uint256 facetsLen = ds.facets.length;
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::20 => /// @notice Prove that a specific arbitrary-length message was sent in a specific L2 block number
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::64 => // Check that the proof length is exactly the same as tree height, to prevent
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::66 => require(_proof.length == L2_TO_L1_LOG_MERKLE_TREE_HEIGHT, "rz");
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::74 => /// @dev Convert arbitrary-length message to the raw l2 log
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::128 => // uint256 baseCost = l2TransactionBaseCost(tx.gasprice, _ergsLimit, uint32(_calldata.length));
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::221 => uint256 factoryDepsLen = _factoryDeps.length;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IMailbox.sol::18 => /// @param reserved The fixed-length fields for usage in a future extension of transaction formats
2022-10-zksync/ethereum/contracts/zksync/interfaces/IMailbox.sol::22 => /// @param paymasterInput The arbitrary-length data that is used as a calldata to the paymaster pre-call
2022-10-zksync/ethereum/contracts/zksync/interfaces/IMailbox.sol::23 => /// @param reservedDynamic The arbitrary-length field for usage in a future extension of transaction formats
2022-10-zksync/ethereum/contracts/zksync/interfaces/IMailbox.sol::38 => // It is also recommended that their length is fixed, since
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::93 => uint256 facetCutsLength = facetCuts.length;
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::100 => require(selectors.length > 0, "B"); // no functions for diamond cut
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::131 => uint256 selectorsLength = _selectors.length;
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::152 => uint256 selectorsLength = _selectors.length;
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::172 => uint256 selectorsLength = _selectors.length;
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::187 => uint256 selectorsLength = ds.facetToSelectors[_facet].selectors.length;
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::190 => ds.facetToSelectors[_facet].facetPosition = uint16(ds.facets.length);
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::207 => uint16 selectorPosition = uint16(ds.facetToSelectors[_facet].selectors.length);
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::232 => uint256 lastSelectorPosition = ds.facetToSelectors[_facet].selectors.length - 1;
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::261 => uint256 lastFacetPosition = ds.facets.length - 1;
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::279 => require(_calldata.length == 0, "H"); // Non-empty calldata for zero address
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::287 => require(data.length == 32, "lp");
2022-10-zksync/ethereum/contracts/zksync/libraries/Merkle.sol::12 => /// NOTE: When using this function, check that the _path length is equal to the tree height to prevent shorter/longer paths attack
2022-10-zksync/ethereum/contracts/zksync/libraries/Merkle.sol::22 => uint256 pathLength = _path.length;
::237 => require(p1.length == p2.length);
::238 => uint256 elements = p1.length;
```
#### Tools used
Manual




================================================================================================


### Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: When dealing with unsigned integer types, comparisons with != 0 are cheaper than with > 0.

Example
🤦 Bad:

// `a` being of type unsigned integer
require(a > 0, "!a > 0");
🚀 Good:

// `a` being of type unsigned integer
require(a != 0, "!a > 0");
#### Findings:
```
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::117 => require(amount > 0, "1T"); // empty deposit amount
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::210 => require(amount > 0, "y1");
::304 => // require(vk.num_inputs > 0); // TODO
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::16 => assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::100 => require(selectors.length > 0, "B"); // no functions for diamond cut
2022-10-zksync/ethereum/contracts/zksync/libraries/Merkle.sol::23 => require(pathLength > 0, "xc");
```
#### Tools used
Manual



================================================================================================


### Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: ⚡️ Only valid for solidity versions <0.6.12 ⚡️

Access roles marked as constant results in computing the keccak256 operation each time the variable is used because assigned operations for constant variables are re-evaluated every time.

Changing the variables to immutable results in computing the hash only once on deployment, leading to gas savings.

Example
🤦 Bad:

bytes32 public constant GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");
🚀 Good:

bytes32 public immutable GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");
#### Findings:
```
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::93 => keccak256(create2Input)
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::283 => bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::69 => keccak256(create2Input)
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::41 => bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::82 => bytes32 data = keccak256(
2022-10-zksync/ethereum/contracts/common/ReentrancyGuard.sol::27 => uint256 private constant LOCK_FLAG_ADDRESS = 0x8e94fed44239eb2314ab7a406345e6c5a8f0ccedf3b600de3d004e672c33abf4; // keccak256("ReentrancyGuard") - 1;
2022-10-zksync/ethereum/contracts/zksync/Config.sol::19 => /// @dev Actually equal to the `keccak256(new bytes(L2_LOG_BYTES))`
2022-10-zksync/ethereum/contracts/zksync/DiamondInit.sol::54 => s.storedBlockHashes[0] = keccak256(abi.encode(storedBlockZero));
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::27 => s.diamondCutStorage.proposedDiamondCutHash = keccak256(abi.encode(_facetCuts, _initAddress));
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol::61 => keccak256(abi.encode(_diamondCut.facetCuts, _diamondCut.initAddress)),
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::85 => return keccak256(abi.encode(_previousBlock.blockHash, _newBlock.newStateRoot));
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::117 => require(keccak256(l2Messages[currentMessage]) == hashedMessage, "k2");
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::122 => chainedPriorityTxsHash = keccak256(abi.encode(chainedPriorityTxsHash, canonicalTxHash));
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::182 => concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::282 => keccak256(
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::355 => bytes32 passThroughDataHash = keccak256(_blockPassThroughData(_newBlockData));
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::356 => bytes32 metadataHash = keccak256(_blockMetaParameters());
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::357 => bytes32 auxiliaryOutputHash = keccak256(_blockAuxilaryOutput(_newBlockData));
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::359 => return keccak256(abi.encode(passThroughDataHash, metadataHash, auxiliaryOutputHash));
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::377 => bytes32 initialStorageChangesHash = keccak256(_block.initialStorageChanges);
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::378 => bytes32 repeatedStorageChangesHash = keccak256(_block.repeatedStorageChanges);
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::379 => bytes32 l2ToL1LogsHash = keccak256(_block.l2Logs);
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::384 => /// @notice Returns the keccak hash of the ABI-encoded StoredBlockInfo
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::386 => return keccak256(abi.encode(_storedBlockInfo));
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::58 => bytes32 hashedLog = keccak256(
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::83 => value: keccak256(_message.data)
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::163 => canonicalTxHash = keccak256(abi.encode(transaction));
2022-10-zksync/ethereum/contracts/zksync/interfaces/IMailbox.sol::91 => /// @param txHash keccak256 hash of encoded transaction representation
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::11 => 0x33774e659306e47509050e97cb651e731180a42d458212294d30751925c551a2; // keccak256("diamond.zksync.init") - 1
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol::14 => bytes32 constant DIAMOND_STORAGE_POSITION = 0xc8fcad8db84d3cc18b4c41d551ea0ee66dd599cde068d998e57d5e09332c131b; // keccak256("diamond.standard.diamond.storage") - 1;
2022-10-zksync/ethereum/contracts/zksync/libraries/Merkle.sol::30 => currentHash = keccak256(abi.encode(currentHash, _path[i]));
2022-10-zksync/ethereum/contracts/zksync/libraries/Merkle.sol::32 => currentHash = keccak256(abi.encode(_path[i], currentHash));
::29 => self.state_0 = keccak256(abi.encodePacked(DST_0, old_state_0, self.state_1, value));
::30 => self.state_1 = keccak256(abi.encodePacked(DST_1, old_state_0, self.state_1, value));
::43 => bytes32 query = keccak256(abi.encodePacked(DST_CHALLENGE, self.state_0, self.state_1, self.challenge_counter));
2022-10-zksync/zksync/contracts/L2ContractHelper.sol::24 => bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
2022-10-zksync/zksync/contracts/L2ContractHelper.sol::37 => bytes32 data = keccak256(
2022-10-zksync/zksync/contracts/bridge/L2ERC20Bridge.sol::114 => bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));
```
#### Tools used
Manual



================================================================================================



### Long Revert Strings

#### Impact
Issue Information: Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

Example
🤦 Bad:

require(condition, "UniswapV3: The reentrancy guard. A transaction cannot re-enter the pool mid-swap");
🚀 Good (with shorter string):

// TODO: Provide link to a reference of error codes
require(condition, "LOK");
🚀 Good (with custom errors):

/// @notice A transaction cannot re-enter the pool mid-swap.
error NoReentrancy();

// ...

if (!condition) {
    revert NoReentrancy();
}

#### Findings:
```
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::5 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::6 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::11 => import "../common/interfaces/IAllowList.sol";
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol::13 => import "../common/libraries/UnsafeBytes.sol";
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::8 => import "../common/interfaces/IAllowList.sol";
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol::10 => import "../common/libraries/UnsafeBytes.sol";
2022-10-zksync/ethereum/contracts/bridge/interfaces/IL1Bridge.sol::5 => import {IMailbox, L2Log, L2Message} from "../../zksync/interfaces/IZkSync.sol";
2022-10-zksync/ethereum/contracts/zksync/DiamondInit.sol::5 => import "../common/interfaces/IAllowList.sol";
::7 => import "../common/libraries/UncheckedMath.sol";
2022-10-zksync/ethereum/contracts/zksync/Storage.sol::6 => import "../common/interfaces/IAllowList.sol";
::5 => import "./Plonk4VerifierWithAccessToDNext.sol";
::6 => import "../common/libraries/UncheckedMath.sol";
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::10 => import "../../common/libraries/UncheckedMath.sol";
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::11 => import "../../common/libraries/UnsafeBytes.sol";
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol::12 => import "../../common/L2ContractHelper.sol";
2022-10-zksync/ethereum/contracts/zksync/facets/Getters.sol::8 => import "../../common/libraries/UncheckedMath.sol";
2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol::6 => import "../../common/L2ContractHelper.sol";
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::10 => import "../../common/libraries/UncheckedMath.sol";
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol::11 => import "../../common/L2ContractHelper.sol";
2022-10-zksync/ethereum/contracts/zksync/libraries/Merkle.sol::5 => import "../../common/libraries/UncheckedMath.sol";
2022-10-zksync/zksync/contracts/bridge/L2ERC20Bridge.sol::5 => import "@openzeppelin/contracts/proxy/beacon/BeaconProxy.sol";
2022-10-zksync/zksync/contracts/bridge/L2ERC20Bridge.sol::6 => import "@openzeppelin/contracts/proxy/beacon/UpgradeableBeacon.sol";
2022-10-zksync/zksync/contracts/bridge/L2ERC20Bridge.sol::10 => import "./interfaces/IL2StandardToken.sol";
2022-10-zksync/zksync/contracts/bridge/L2ETHBridge.sol::9 => import "./interfaces/IL2EthInitializable.sol";
2022-10-zksync/zksync/contracts/bridge/L2ETHBridge.sol::10 => import "./interfaces/IL2StandardToken.sol";
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol::5 => import "@openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol";
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol::6 => import "./interfaces/IL2StandardToken.sol";
```
#### Tools used
Manual



================================================================================================


### Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

Example
🤦 Bad:

uint256 b = a / 2;
uint256 c = a / 4;
uint256 d = a * 8;
🚀 Good:

uint256 b = a >> 1;
uint256 c = a >> 2;
uint256 d = a << 3;

#### Findings:
```
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::26 => uint160 constant SYSTEM_CONTRACTS_OFFSET = 0x8000; // 2^15
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol::72 => codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
2022-10-zksync/ethereum/contracts/common/libraries/UnsafeBytes.sol::14 => * 2) Read variables are not cleaned up - https://docs.soliditylang.org/en/v0.8.16/internals/variable_cleanup.html.
2022-10-zksync/ethereum/contracts/zksync/Config.sol::23 => uint256 constant INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + 64 * 4896;
2022-10-zksync/zksync/contracts/L2ContractHelper.sol::17 => uint160 constant SYSTEM_CONTRACTS_OFFSET = 0x8000; // 2^15
```
#### Tools used
Manual


