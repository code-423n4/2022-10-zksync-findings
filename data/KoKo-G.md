# GAS ISSUES FOR [2022-10-ZKSYNC](https://github.com/code-423n4/2022-10-zksync/tree/main)

## [G-01] If a function is not open to any user, it can be marked as `payable` to save gas

Description: Under the hood the compiler checks if a function is payable. The check is skipped if it is marked as such by the developer

./ethereum/contracts/zksync/facets/Governance.sol

```
L28:   function acceptGovernor() external {
```

./zksync/contracts/bridge/L2StandardERC20.sol

```
L102:  function bridgeMint(address _to, uint256 _amount) external override onlyBridge {

L109:  function bridgeBurn(address _from, uint256 _amount) external override onlyBridge {
```

./ethereum/contracts/common/AllowList.sol

```
L65:   function setBatchPublicAccess(address[] calldata _targets, bool[] calldata _enables) external onlyOwner {

L140:  function setPendingOwner(address _newPendingOwner) external onlyOwner {

L153:  function acceptOwner() external {
```

## [G-02] `uncheck` the `i++`/`i--` in for loops since there's no way to overflow/underflow

./ethereum/contracts/zksync/libraries/Diamond.sol

```
L132:  for (uint256 i = 0; i < selectorsLength; ++i) {

L153:  for (uint256 i = 0; i < selectorsLength; ++i) {

L173:  for (uint256 i = 0; i < selectorsLength; ++i) {
```

## [G-03] Use default values of variables types

Description: uint256 - 0;, string - "";, address - address(0);, etc.

./ethereum/contracts/zksync/libraries/Merkle.sol

```
L28:   for (uint256 i = 0; i < pathLength; i = i.uncheckedInc()) {
```

./ethereum/contracts/zksync/libraries/Diamond.sol

```
L132:  for (uint256 i = 0; i < selectorsLength; ++i) {

L153:  for (uint256 i = 0; i < selectorsLength; ++i) {

L173:  for (uint256 i = 0; i < selectorsLength; ++i) {
```

./ethereum/contracts/common/AllowList.sol

```
L69:   for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {

L103:  for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
```

./ethereum/contracts/zksync/facets/Getters.sol

```
L163:  for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
```

./zksync/contracts/bridge/L2ETHBridge.sol

```
L28:   address constant CONVENTIONAL_ETH_ADDRESS = address(0);
```

./ethereum/contracts/zksync/facets/Mailbox.sol

```
L223:  for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
```

./ethereum/contracts/zksync/facets/Executor.sol

```
L111:  for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {

L162:  for (uint256 i = 0; i < blocksLength; i = i.uncheckedInc()) {

L210:  for (uint256 i = 0; i < nBlocks; i = i.uncheckedInc()) {

L240:  for (uint256 i = 0; i < committedBlocksLength; i = i.uncheckedInc()) {
```

## [G-04] Use `immutable` instead of `constant` if the variable is hash or any result from a complex operation

Description: The calculation is made every time the variable is used (not only when the contract is compiled and deployed)

./ethereum/contracts/common/L2ContractHelper.sol

```
L41:   bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
```

./zksync/contracts/L2ContractHelper.sol

```
L24:   bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
```

## [G-05] Use `A << 1` and `A >> 1` instead of `A * 2` and `A / 2`

Description: saves around 6-8 gas

./ethereum/contracts/common/L2ContractHelper.sol

```
L53:   require(bytecodeLenInWords < 2**16, "pp");

L72:   codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
```

./ethereum/contracts/zksync/libraries/Merkle.sol

```
L25:   require(_index < 2**pathLength, "pz");
```

./ethereum/contracts/zksync/Config.sol

```
L23:   uint256 constant INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + 64 * 4896;
```

## [G-06] Skip copying data from calldata to memory for function parameters

Description: Use `calldata` location for reference-type input args

./zksync/contracts/L2ContractHelper.sol

```
L26:   function sendMessageToL1(bytes memory _message) internal returns (bytes32) {
```

./ethereum/contracts/bridge/L1ERC20Bridge.sol

```
L260:  function _parseL2WithdrawalMessage(bytes memory _l2ToL1message)
```

./ethereum/contracts/common/L2ContractHelper.sol

```
L43:   function sendMessageToL1(bytes memory _message) internal returns (bytes32) {

L47:   function hashL2Bytecode(bytes memory _bytecode) internal pure returns (bytes32 hashedBytecode) {
```

./zksync/contracts/bridge/L2StandardERC20.sol

```
L43:   function bridgeInitialize(address _l1Address, bytes memory _data) external initializer {
```

./zksync/contracts/ExternalDecoder.sol

```
       /// @audit Store `_input` in calldata.
L10:   function decodeString(bytes memory _input) external pure returns (string memory result) {

L15:   function decodeUint8(bytes memory _input) external pure returns (uint8 result) {
```

./ethereum/contracts/zksync/libraries/PriorityQueue.sol

```
L54:   function pushBack(Queue storage _queue, PriorityOperation memory _operation) internal {
```

./ethereum/contracts/bridge/L1EthBridge.sol

```
L214:  function _parseL2WithdrawalMessage(bytes memory _message)
```

./ethereum/contracts/zksync/facets/Mailbox.sol

```
L43:   L2Log memory _log,

L53:   L2Log memory _log,
```

./ethereum/contracts/zksync/facets/Executor.sol

```
L23:   function _commitOneBlock(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)

L80:   function _calculateBlockHash(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)

L190:  function _executeOneBlock(StoredBlockInfo memory _storedBlock, uint256 _executedBlockIdx) internal {

L278:  VerifierParams memory _verifierParams

L385:  function _hashStoredBlockInfo(StoredBlockInfo memory _storedBlockInfo) internal pure returns (bytes32) {
```

./ethereum/contracts/common/libraries/UnsafeBytes.sol

```
L18:   function readUint32(bytes memory _bytes, uint256 _start) internal pure returns (uint32 result, uint256 offset) {

L25:   function readAddress(bytes memory _bytes, uint256 _start) internal pure returns (address result, uint256 offset) {

L32:   function readUint256(bytes memory _bytes, uint256 _start) internal pure returns (uint256 result, uint256 offset) {
```

./ethereum/contracts/zksync/libraries/Diamond.sol

```
L89:   function diamondCut(DiamondCutData memory _diamondCut) internal {

L121:  bytes4[] memory _selectors,

L145:  bytes4[] memory _selectors,

L277:  function _initializeDiamondCut(address _init, bytes memory _calldata) private {
```

## [G-07] Inline internal functions to save gas

./ethereum/contracts/zksync/libraries/Diamond.sol

```
L119:  function _addFunctions(

L143:  function _replaceFunctions(

L167:  function _removeFunctions(address _facet, bytes4[] memory _selectors) private {

L256:  function _removeFacet(address _facet) private {
```

./ethereum/contracts/common/ReentrancyGuard.sol

```
L43:   function _initializeReentrancyGuard() private {
```

## [G-08] Mark contract constants with the `constant` and `immutable` keywords to save gas

./zksync/contracts/bridge/L2StandardERC20.sol

```
L24:   ERC20Getters availableGetters;

L32:   address public override l2Bridge;

L35:   address public override l1Address;
```

./zksync/contracts/bridge/L2ERC20Bridge.sol

```
L19:   address public override l1Bridge;

L23:   UpgradeableBeacon public l2TokenFactory;

L26:   bytes32 l2TokenProxyBytecodeHash;
```

./ethereum/contracts/zksync/facets/Base.sol

```
L12:   AppStorage internal s;
```

./zksync/contracts/bridge/L2ETHBridge.sol

```
L16:   uint256 public totalSupply;

L22:   address public override l1Bridge;
```

## [G-09] Spread the `require()` conditions to multiple `require()` statements to save gas from `&&`

./ethereum/contracts/common/AllowList.sol

```
L96:   require(
L97:   callersLength == _targets.length &&
L98:   callersLength == _functionSigs.length &&
L99:   callersLength == _enables.length,
L100:  "yw"
L101:  );
```

./ethereum/contracts/common/L2ContractHelper.sol

```
L65:   require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf");
```

## [G-10] A < B + 1 is cheaper than A <= B

./ethereum/contracts/zksync/facets/Mailbox.sol

```
L56:   require(_blockNumber <= s.totalBlocksExecuted, "xx");

L124:  require(_ergsLimit <= PRIORITY_TX_MAX_ERGS_LIMIT, "ui");
```

./ethereum/contracts/zksync/facets/DiamondCut.sol

```
L49:   bool approvedBySecurityCouncil = s.diamondCutStorage.securityCouncilEmergencyApprovals >=

L52:   bool upgradeNoticePeriodPassed = block.timestamp >=
```

./ethereum/contracts/zksync/DiamondProxy.sol

```
L24:   require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
```

./ethereum/contracts/zksync/facets/Executor.sol

```
L51:   bool timestampNotTooBig = l2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA;

L216:  require(s.totalBlocksExecuted <= s.totalBlocksVerified, "n");

L268:  require(currentTotalBlocksVerified <= s.totalBlocksCommitted, "q");
```
