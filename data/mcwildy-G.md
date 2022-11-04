# GAS OPTIMIZATIONS REPORT

## ZKSYNC

### Use `constant` and `immutable` keywords for storage varibles if they doesn't change through contract's lifecycle

That way the variable will go to the contract's bytecode and will not allocate a storage slot

[L2ERC20Bridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol):

```solidity
line#L19:  address public override l1Bridge;

line#L23:  UpgradeableBeacon public l2TokenFactory;

line#L26:  bytes32 l2TokenProxyBytecodeHash;
```

[Base.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Base.sol):

```solidity
line#L12:  AppStorage internal s;
```

[L2StandardERC20.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol):

```solidity
line#L24:  ERC20Getters availableGetters;

line#L29:  uint8 private decimals_;

line#L32:  address public override l2Bridge;

line#L35:  address public override l1Address;
```

[L2ETHBridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol):

```solidity
line#L16:  uint256 public totalSupply;

line#L22:  address public override l1Bridge;
```

### Bitshifting is cheaper than multiplication

[Config.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Config.sol):

```solidity
line#L23:  uint256 constant INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + 64 * 4896;
```

[Merkle.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Merkle.sol):

```solidity
line#L25:  require(_index < 2**pathLength, "pz");
```

[L2ContractHelper.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol):

```solidity
line#L53:  require(bytecodeLenInWords < 2**16, "pp");

line#L72:  codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
```

### If a `constant` variable initialization includes `keccak256()`, `sha256()`, etc. it should be marked as `immutable` instead

The result of the calculation of the hash (or whatever the expression is) is expected to be saved once compiled, but that's not the case. If marked as `constant` the calculation will be executed every time the variable is accessed.

[L2ContractHelper.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/L2ContractHelper.sol):

```solidity
line#L24:  bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
```

[L2ContractHelper.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol):

```solidity
line#L41:  bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
```

### Use `uint256` instead of the smaller uints where possible

The word-size in ethereum is 32 bytes which means that every variables which is sized with less bytes will cost more to operate with

[L2ContractHelper.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol):

```solidity
line#L26:  uint160 constant SYSTEM_CONTRACTS_OFFSET = 0x8000;

line#L64:  uint8 version = uint8(_bytecodeHash[0]);
```

[Storage.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Storage.sol):

```solidity
line#L40:  uint8 l2ShardId;

line#L42:  uint16 txNumberInBlock;

line#L54:  uint16 txNumberInBlock;
```

[DiamondInit.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondInit.sol):

```solidity
line#L30:  uint64 _genesisIndexRepeatedStorageChanges,
```

[L2ContractHelper.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/L2ContractHelper.sol):

```solidity
line#L17:  uint160 constant SYSTEM_CONTRACTS_OFFSET = 0x8000;
```

[IMailbox.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IMailbox.sol):

```solidity
line#L86:  uint32 _calldataLength

line#L98:  uint64 expirationBlock,
```

[IExecutor.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IExecutor.sol):

```solidity
line#L16:  uint64 blockNumber;

line#L18:  uint64 indexRepeatedStorageChanges;

line#L42:  uint64 timestamp;

line#L43:  uint64 indexRepeatedStorageChanges;
```

[Mailbox.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol):

```solidity
line#L125: uint64 expirationBlock = uint64(block.number + PRIORITY_EXPIRATION);

line#L150: uint64 _expirationBlock,
```

[IL1Bridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/interfaces/IL1Bridge.sol):

```solidity
line#L29:  uint16 _l2TxNumberInBlock,

line#L36:  uint16 _l2TxNumberInBlock,
```

[Diamond.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol):

```solidity
line#L24:  uint16 selectorPosition;

line#L33:  uint16 facetPosition;

line#L207: uint16 selectorPosition = uint16(ds.facetToSelectors[_facet].selectors.length);
```

[ExternalDecoder.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/ExternalDecoder.sol):

```solidity
line#L15:  function decodeUint8(bytes memory _input) external pure returns (uint8 result) {
```

[UnsafeBytes.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/libraries/UnsafeBytes.sol):

```solidity
line#L18:  function readUint32(bytes memory _bytes, uint256 _start) internal pure returns (uint32 result, uint256 offset) {
```

[L2StandardERC20.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol):

```solidity
line#L12:  event BridgeInitialization(address indexed l1Token, string name, string symbol, uint8 decimals);

line#L29:  uint8 private decimals_;

line#L84:  try ExternalDecoder.decodeUint8(decimalsBytes) returns (uint8 decimalsUint8) {
```

[L1ERC20Bridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol):

```solidity
line#L185: uint16 _l2TxNumberInBlock,

line#L228: uint16 _l2TxNumberInBlock,

line#L273: (uint32 functionSignature, uint256 offset) = UnsafeBytes.readUint32(_l2ToL1message, 0);
```

[L1EthBridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol):

```solidity
line#L138: uint16 _l2TxNumberInBlock,

line#L185: uint16 _l2TxNumberInBlock,

line#L223: (uint32 functionSignature, uint256 offset) = UnsafeBytes.readUint32(_message, 0);
```

[PriorityQueue.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol):

```solidity
line#L12:  uint64 expirationBlock;
```

[IL1Bridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/interfaces/IL1Bridge.sol):

```solidity
line#L10:  uint16 _l2TxNumberInBlock,
```

### Use `unchecked` for the counter in `for` loops

Since it is compared to a target value (usually the length of an array) there is no way the counter overflows

[Diamond.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol):

```solidity
line#L94:  for (uint256 i = 0; i < facetCutsLength; ++i) {

line#L132: for (uint256 i = 0; i < selectorsLength; ++i) {

line#L153: for (uint256 i = 0; i < selectorsLength; ++i) {

line#L173: for (uint256 i = 0; i < selectorsLength; ++i) {
```

### `x < y + 1` is cheaper than `x <= y`

[Executor.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol):

```solidity
line#L50:  bool timestampNotTooSmall = block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= l2BlockTimestamp;

line#L51:  bool timestampNotTooBig = l2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA;

line#L216: require(s.totalBlocksExecuted <= s.totalBlocksVerified, "n");

line#L268: require(currentTotalBlocksVerified <= s.totalBlocksCommitted, "q");
```

[DiamondProxy.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondProxy.sol):

```solidity
line#L24:  require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
```

[DiamondCut.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol):

```solidity
line#L49:  bool approvedBySecurityCouncil = s.diamondCutStorage.securityCouncilEmergencyApprovals >=

line#L52:  bool upgradeNoticePeriodPassed = block.timestamp >=
```

[Mailbox.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol):

```solidity
line#L56:  require(_blockNumber <= s.totalBlocksExecuted, "xx");

line#L124: require(_ergsLimit <= PRIORITY_TX_MAX_ERGS_LIMIT, "ui");
```

### Mark functions as `payable` to avoid the low-level evm `is-a-payment-provided` check

The EVM checks whether a payment is provided to the function and this check costs additional gas. If the function is marked as `payable` there is no such check

[L2StandardERC20.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol):

```solidity
line#L102: function bridgeMint(address _to, uint256 _amount) external override onlyBridge {

line#L109: function bridgeBurn(address _from, uint256 _amount) external override onlyBridge {
```

[AllowList.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol):

```solidity
line#L57:  function setPublicAccess(address _target, bool _enable) external onlyOwner {

line#L65:  function setBatchPublicAccess(address[] calldata _targets, bool[] calldata _enables) external onlyOwner {

line#L140: function setPendingOwner(address _newPendingOwner) external onlyOwner {

line#L153: function acceptOwner() external {
```

[Governance.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol):

```solidity
line#L28:  function acceptGovernor() external {
```

### Function inlining

If a function is called only once it can be inlined in order to save 20 gas

[ReentrancyGuard.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/ReentrancyGuard.sol):

```solidity
line#L43:  function _initializeReentrancyGuard() private {
```

[Diamond.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol):

```solidity
line#L119: function _addFunctions(

line#L143: function _replaceFunctions(

line#L256: function _removeFacet(address _facet) private {

line#L277: function _initializeDiamondCut(address _init, bytes memory _calldata) private {
```

### Use `if (x != 0)` instead of `if (x > 0)` for uints comparison

Saves 3 gas per `if`-check

[L1ERC20Bridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol):

```solidity
line#L117: require(amount > 0, "1T");

line#L210: require(amount > 0, "y1");
```

[Merkle.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Merkle.sol):

```solidity
line#L23:  require(pathLength > 0, "xc");
```

### Do not write default values to variables

If you declare a variables of type `uint256` it will automatically get assigned to its default value of 0. It is a gas wastage to assign it yourself.

[Executor.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol):

```solidity
line#L111: for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {

line#L162: for (uint256 i = 0; i < blocksLength; i = i.uncheckedInc()) {

line#L210: for (uint256 i = 0; i < nBlocks; i = i.uncheckedInc()) {

line#L240: for (uint256 i = 0; i < committedBlocksLength; i = i.uncheckedInc()) {
```

[AllowList.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol):

```solidity
line#L69:  for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {

line#L103: for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
```

[Diamond.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol):

```solidity
line#L94:  for (uint256 i = 0; i < facetCutsLength; ++i) {

line#L132: for (uint256 i = 0; i < selectorsLength; ++i) {

line#L153: for (uint256 i = 0; i < selectorsLength; ++i) {

line#L173: for (uint256 i = 0; i < selectorsLength; ++i) {
```

[Merkle.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Merkle.sol):

```solidity
line#L28:  for (uint256 i = 0; i < pathLength; i = i.uncheckedInc()) {
```

[L2ETHBridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol):

```solidity
line#L28:  address constant CONVENTIONAL_ETH_ADDRESS = address(0);
```

[Getters.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Getters.sol):

```solidity
line#L163: for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
```

[Mailbox.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol):

```solidity
line#L223: for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
```

### Avoid using `&&` in `require()` or `if()` statements

Use multiple `require()`/`if` statements instead

[AllowList.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol):

```solidity
line#L96:  require(
line#L97:  callersLength == _targets.length &&
line#L98:  callersLength == _functionSigs.length &&
line#L99:  callersLength == _enables.length,
line#L100: "yw"
line#L101: );
```

[L2ContractHelper.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol):

```solidity
line#L65:  require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf");
```

### Use custom errors instead of `require` strings

[Governance.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol):

```solidity
line#L30:  require(msg.sender == pendingGovernor, "n4");
```

[Base.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Base.sol):

```solidity
line#L16:  require(msg.sender == s.governor, "1g");

line#L22:  require(s.validators[msg.sender], "1h");
```

[AllowListed.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowListed.sol):

```solidity
line#L15:  require(_allowList.canCall(msg.sender, address(this), functionSig), "nr");
```

[Executor.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol):

```solidity
line#L28:  require(_newBlock.blockNumber == _previousBlock.blockNumber + 1, "f");

line#L39:  require(_previousBlock.blockHash == previousBlockHash, "l");

line#L52:  require(timestampNotTooSmall, "h");

line#L53:  require(timestampNotTooBig, "h1");

line#L117: require(keccak256(l2Messages[currentMessage]) == hashedMessage, "k2");

line#L129: require(!isSystemContextLogProcessed, "fx");

line#L142: require(currentBytecode == factoryDeps.length, "ym");

line#L143: require(currentMessage == l2Messages.length, "pl");

line#L159: require(s.storedBlockHashes[s.totalBlocksCommitted] == _hashStoredBlockInfo(_lastCommittedBlockData), "i");

line#L192: require(currentBlockNumber == s.totalBlocksExecuted + _executedBlockIdx + 1, "k");

line#L199: require(priorityOperationsHash == _storedBlock.priorityOperationsHash, "x");

line#L216: require(s.totalBlocksExecuted <= s.totalBlocksVerified, "n");

line#L241: require(
line#L242: _hashStoredBlockInfo(_committedBlocks[i]) ==
line#L243: s.storedBlockHashes[currentTotalBlocksVerified.uncheckedInc()],
line#L244: "o1"
line#L245: );

line#L261: require(successVerifyProof, "p");

line#L268: require(currentTotalBlocksVerified <= s.totalBlocksCommitted, "q");

line#L337: require(s.totalBlocksCommitted > _newLastBlock, "v1");
```

[ReentrancyGuard.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/ReentrancyGuard.sol):

```solidity
line#L55:  require(lockSlotOldValue == 0, "1B");

line#L72:  require(_status == _NOT_ENTERED, "r1");
```

[L2ERC20Bridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol):

```solidity
line#L58:  require(msg.sender == l1Bridge, "mq");

line#L63:  require(deployedToken == expectedL2Token, "mt");

line#L95:  require(l1Token != address(0), "yh");
```

[Merkle.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Merkle.sol):

```solidity
line#L23:  require(pathLength > 0, "xc");

line#L24:  require(pathLength < 256, "bt");

line#L25:  require(_index < 2**pathLength, "pz");
```

[L2StandardERC20.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol):

```solidity
line#L44:  require(l1Address == address(0), "in5");

line#L45:  require(_l1Address != address(0), "in6");
```

[L2ETHBridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol):

```solidity
line#L49:  require(msg.sender == l1Bridge, "ni");

line#L64:  require(_l2Token == CONVENTIONAL_ETH_ADDRESS, "zn");
```

[AllowList.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol):

```solidity
line#L33:  require(_owner != address(0), "kq");

line#L38:  require(msg.sender == owner, "kx");

line#L96:  require(
line#L97:  callersLength == _targets.length &&
line#L98:  callersLength == _functionSigs.length &&
line#L99:  callersLength == _enables.length,
line#L100: "yw"
line#L101: );

line#L155: require(msg.sender == newOwner, "n0");
```

[Diamond.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol):

```solidity
line#L100: require(selectors.length > 0, "B");

line#L109: revert("C");

line#L135: require(oldFacet.facetAddress == address(0), "J");

line#L150: require(_facet != address(0), "K");

line#L170: require(_facet == address(0), "a1");

line#L176: require(oldFacet.facetAddress != address(0), "a2");

line#L279: require(_calldata.length == 0, "H");

line#L283: require(success, "I");

line#L288: require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");
```

[Mailbox.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol):

```solidity
line#L56:  require(_blockNumber <= s.totalBlocksExecuted, "xx");

line#L63:  require(hashedLog != L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH, "tw");

line#L66:  require(_proof.length == L2_TO_L1_LOG_MERKLE_TREE_HEIGHT, "rz");

line#L124: require(_ergsLimit <= PRIORITY_TX_MAX_ERGS_LIMIT, "ui");
```

[DiamondCut.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol):

```solidity
line#L23:  require(s.diamondCutStorage.proposedDiamondCutTimestamp == 0, "a3");

line#L40:  require(_resetProposal(), "g1");

line#L56:  require(approvedBySecurityCouncil || !diamondStorage.isFrozen, "f3");

line#L59:  require(
line#L60:  s.diamondCutStorage.proposedDiamondCutHash ==
line#L61:  keccak256(abi.encode(_diamondCut.facetCuts, _diamondCut.initAddress)),
line#L62:  "a4"
line#L63:  );

line#L80:  require(!diamondStorage.isFrozen, "a9");

line#L94:  require(diamondStorage.isFrozen, "a7");

line#L108: require(s.diamondCutStorage.securityCouncilMemberLastApprovedProposalId[msg.sender] < currentProposalId, "ao");

line#L111: require(s.diamondCutStorage.proposedDiamondCutTimestamp != 0, "f0");
```

[DiamondProxy.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondProxy.sol):

```solidity
line#L13:  require(_chainId == block.chainid, "pr");

line#L24:  require(msg.data.length >= 4 || msg.data.length == 0, "Ut");

line#L29:  require(facetAddress != address(0), "F");

line#L30:  require(!diamondStorage.isFrozen || !facet.isFreezable, "q1");
```

[L1EthBridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol):

```solidity
line#L93:  require(_l1Token == CONVENTIONAL_ETH_ADDRESS, "bx");

line#L141: require(_l1Token == CONVENTIONAL_ETH_ADDRESS, "sj");

line#L189: require(!isWithdrawalFinalized[_l2BlockNumber][_l2MessageIndex], "jj");

line#L205: require(success, "rj");
```

[PriorityQueue.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol):

```solidity
line#L64:  require(!_queue.isEmpty(), "D");

line#L72:  require(!_queue.isEmpty(), "s");
```

[L2ContractHelper.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol):

```solidity
line#L50:  require(_bytecode.length % 32 == 0, "po");

line#L53:  require(bytecodeLenInWords < 2**16, "pp");

line#L65:  require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf");

line#L67:  require(bytecodeLen(_bytecodeHash) % 2 == 1, "uy");
```

[Getters.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Getters.sol):

```solidity
line#L148: require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");
```

[L1ERC20Bridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol):

```solidity
line#L77:  require(_factoryDeps.length == 2, "mk");

line#L117: require(amount > 0, "1T");

line#L210: require(amount > 0, "y1");

line#L232: require(!isWithdrawalFinalized[_l2BlockNumber][_l2MessageIndex], "pw");

line#L271: require(_l2ToL1message.length == 76, "kk");

line#L274: require(bytes4(functionSignature) == this.finalizeWithdrawal.selector, "nt");
```

### Use `calldata` where possible instead of `memory` to save gas

[ExternalDecoder.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/ExternalDecoder.sol):

```solidity
           /// @audit Store `_input` in calldata.
line#L10:  function decodeString(bytes memory _input) external pure returns (string memory result) {

line#L15:  function decodeUint8(bytes memory _input) external pure returns (uint8 result) {
```

[L1ERC20Bridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol):

```solidity
line#L260: function _parseL2WithdrawalMessage(bytes memory _l2ToL1message)
```

[PriorityQueue.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol):

```solidity
line#L54:  function pushBack(Queue storage _queue, PriorityOperation memory _operation) internal {
```

[L2ContractHelper.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/L2ContractHelper.sol):

```solidity
line#L26:  function sendMessageToL1(bytes memory _message) internal returns (bytes32) {
```

[UnsafeBytes.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/libraries/UnsafeBytes.sol):

```solidity
line#L18:  function readUint32(bytes memory _bytes, uint256 _start) internal pure returns (uint32 result, uint256 offset) {

line#L25:  function readAddress(bytes memory _bytes, uint256 _start) internal pure returns (address result, uint256 offset) {

line#L32:  function readUint256(bytes memory _bytes, uint256 _start) internal pure returns (uint256 result, uint256 offset) {

line#L39:  function readBytes32(bytes memory _bytes, uint256 _start) internal pure returns (bytes32 result, uint256 offset) {
```

[L1EthBridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol):

```solidity
line#L214: function _parseL2WithdrawalMessage(bytes memory _message)
```

[Mailbox.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol):

```solidity
line#L43:  L2Log memory _log,

line#L53:  L2Log memory _log,
```

[Executor.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol):

```solidity
line#L23:  function _commitOneBlock(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)

line#L80:  function _calculateBlockHash(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)

line#L190: function _executeOneBlock(StoredBlockInfo memory _storedBlock, uint256 _executedBlockIdx) internal {

line#L278: VerifierParams memory _verifierParams
```

[L2StandardERC20.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol):

```solidity
line#L43:  function bridgeInitialize(address _l1Address, bytes memory _data) external initializer {
```

[L2ContractHelper.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol):

```solidity
line#L43:  function sendMessageToL1(bytes memory _message) internal returns (bytes32) {

line#L47:  function hashL2Bytecode(bytes memory _bytecode) internal pure returns (bytes32 hashedBytecode) {
```

[Diamond.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol):

```solidity
line#L89:  function diamondCut(DiamondCutData memory _diamondCut) internal {

line#L121: bytes4[] memory _selectors,

line#L167: function _removeFunctions(address _facet, bytes4[] memory _selectors) private {

line#L277: function _initializeDiamondCut(address _init, bytes memory _calldata) private {
```
