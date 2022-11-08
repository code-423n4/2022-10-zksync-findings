# 2022-10-ZKSYNC

## Gas Optimizations Report

### `internal` and `private` functions that are called only once should be inlined.

The execution of a non-inlined function would cost up to 40 more gas because of two extra `jump`s as well as some other instructions.

_There are **6** instances of this issue:_

```solidity
File: ethereum/contracts/common/ReentrancyGuard.sol

43:   function _initializeReentrancyGuard() private {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/ReentrancyGuard.sol

```solidity
File: ethereum/contracts/zksync/libraries/Diamond.sol

119:  function _addFunctions(

143:  function _replaceFunctions(

167:  function _removeFunctions(address _facet, bytes4[] memory _selectors) private {

256:  function _removeFacet(address _facet) private {

277:  function _initializeDiamondCut(address _init, bytes memory _calldata) private {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol

### Using `!= 0` on `uints` costs less gas than `> 0`.

This change saves 3 gas per instance/loop

_There are **3** instances of this issue:_

```solidity
File: ethereum/contracts/bridge/L1ERC20Bridge.sol

117:   require(amount > 0, "1T");

210:   require(amount > 0, "y1");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol

```solidity
File: ethereum/contracts/zksync/libraries/Merkle.sol

23:    require(pathLength > 0, "xc");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Merkle.sol

### It costs more gas to initialize non-`constant`/non-`immutable` variables to zero than to let the default of zero be applied

Not overwriting the default for stack variables saves 8 gas. Storage and memory variables have larger savings

_There are **15** instances of this issue:_

```solidity
File: ethereum/contracts/common/AllowList.sol

69:    for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {

103:  for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol

```solidity
File: ethereum/contracts/zksync/facets/Executor.sol

111:  for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {

162:  for (uint256 i = 0; i < blocksLength; i = i.uncheckedInc()) {

180:  for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {

210:  for (uint256 i = 0; i < nBlocks; i = i.uncheckedInc()) {

240:  for (uint256 i = 0; i < committedBlocksLength; i = i.uncheckedInc()) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol

```solidity
File: ethereum/contracts/zksync/facets/Getters.sol

163:  for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Getters.sol

```solidity
File: ethereum/contracts/zksync/facets/Mailbox.sol

223:  for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol

```solidity
File: ethereum/contracts/zksync/libraries/Diamond.sol

94:    for (uint256 i = 0; i < facetCutsLength; ++i) {

132:  for (uint256 i = 0; i < selectorsLength; ++i) {

153:  for (uint256 i = 0; i < selectorsLength; ++i) {

173:  for (uint256 i = 0; i < selectorsLength; ++i) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol

```solidity
File: ethereum/contracts/zksync/libraries/Merkle.sol

28:    for (uint256 i = 0; i < pathLength; i = i.uncheckedInc()) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Merkle.sol

```solidity
File: zksync/contracts/bridge/L2ETHBridge.sol

28:   address constant CONVENTIONAL_ETH_ADDRESS = address(0);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol

### Functions that are access-restricted from most users may be marked as `payable`

Marking a function as `payable` reduces gas cost since the compiler does not have to check whether a payment was provided or not. This change will save around 21 gas per function call.

_There are **7** instances of this issue:_

```solidity
File: ethereum/contracts/common/AllowList.sol

57:   function setPublicAccess(address _target, bool _enable) external onlyOwner {

65:   function setBatchPublicAccess(address[] calldata _targets, bool[] calldata _enables) external onlyOwner {

140:  function setPendingOwner(address _newPendingOwner) external onlyOwner {

153:  function acceptOwner() external {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol

```solidity
File: ethereum/contracts/zksync/facets/Governance.sol

28:   function acceptGovernor() external {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol

```solidity
File: zksync/contracts/bridge/L2StandardERC20.sol

102:  function bridgeMint(address _to, uint256 _amount) external override onlyBridge {

109:  function bridgeBurn(address _from, uint256 _amount) external override onlyBridge {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol

### `++i`/`i++` should be `unchecked{++I}`/`unchecked{I++}` in `for`-loops

When an increment or any arithmetic operation is not possible to overflow it should be placed in `unchecked{}` block. \This is because of the default compiler overflow and underflow safety checks since Solidity version 0.8.0. \In for-loops it saves around 30-40 gas **per loop**

_There are **4** instances of this issue:_

```solidity
File: ethereum/contracts/zksync/libraries/Diamond.sol

94:    for (uint256 i = 0; i < facetCutsLength; ++i) {

132:  for (uint256 i = 0; i < selectorsLength; ++i) {

153:  for (uint256 i = 0; i < selectorsLength; ++i) {

173:  for (uint256 i = 0; i < selectorsLength; ++i) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol

### Usage of `uint`s/`int`s smaller than 32 bytes (256 bits) incurs overhead

'When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.' \ https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html \ Use a larger size then downcast where needed

_There are **34** instances of this issue:_

```solidity
File: ethereum/contracts/bridge/L1ERC20Bridge.sol

185:  uint16 _l2TxNumberInBlock,

228:  uint16 _l2TxNumberInBlock,

273:  (uint32 functionSignature, uint256 offset) = UnsafeBytes.readUint32(_l2ToL1message, 0);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol

```solidity
File: ethereum/contracts/bridge/L1EthBridge.sol

138:  uint16 _l2TxNumberInBlock,

185:  uint16 _l2TxNumberInBlock,

223:  (uint32 functionSignature, uint256 offset) = UnsafeBytes.readUint32(_message, 0);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol

```solidity
File: ethereum/contracts/bridge/interfaces/IL1Bridge.sol

29:    uint16 _l2TxNumberInBlock,

36:    uint16 _l2TxNumberInBlock,
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/interfaces/IL1Bridge.sol

```solidity
File: ethereum/contracts/common/L2ContractHelper.sol

26:    uint160 constant SYSTEM_CONTRACTS_OFFSET = 0x8000;

64:    uint8 version = uint8(_bytecodeHash[0]);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol

```solidity
File: ethereum/contracts/common/libraries/UnsafeBytes.sol

18:   function readUint32(bytes memory _bytes, uint256 _start) internal pure returns (uint32 result, uint256 offset) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/libraries/UnsafeBytes.sol

```solidity
File: ethereum/contracts/zksync/DiamondInit.sol

30:    uint64 _genesisIndexRepeatedStorageChanges,
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondInit.sol

```solidity
File: ethereum/contracts/zksync/Storage.sol

40:   uint8 l2ShardId;

42:   uint16 txNumberInBlock;

54:   uint16 txNumberInBlock;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Storage.sol

```solidity
File: ethereum/contracts/zksync/facets/Mailbox.sol

125:  uint64 expirationBlock = uint64(block.number + PRIORITY_EXPIRATION);

150:  uint64 _expirationBlock,
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol

```solidity
File: ethereum/contracts/zksync/interfaces/IExecutor.sol

16:    uint64 blockNumber;

18:    uint64 indexRepeatedStorageChanges;

41:    uint64 blockNumber;

42:    uint64 timestamp;

43:    uint64 indexRepeatedStorageChanges;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IExecutor.sol

```solidity
File: ethereum/contracts/zksync/interfaces/IMailbox.sol

86:    uint32 _calldataLength

98:    uint64 expirationBlock,
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IMailbox.sol

```solidity
File: ethereum/contracts/zksync/libraries/Diamond.sol

24:   uint16 selectorPosition;

33:   uint16 facetPosition;

207:  uint16 selectorPosition = uint16(ds.facetToSelectors[_facet].selectors.length);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol

```solidity
File: ethereum/contracts/zksync/libraries/PriorityQueue.sol

12:   uint64 expirationBlock;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol

```solidity
File: zksync/contracts/ExternalDecoder.sol

15:   function decodeUint8(bytes memory _input) external pure returns (uint8 result) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/ExternalDecoder.sol

```solidity
File: zksync/contracts/L2ContractHelper.sol

17:   uint160 constant SYSTEM_CONTRACTS_OFFSET = 0x8000;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/L2ContractHelper.sol

```solidity
File: zksync/contracts/bridge/L2StandardERC20.sol

12:   event BridgeInitialization(address indexed l1Token, string name, string symbol, uint8 decimals);

29:   uint8 private decimals_;

84:    try ExternalDecoder.decodeUint8(decimalsBytes) returns (uint8 decimalsUint8) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol

```solidity
File: zksync/contracts/bridge/interfaces/IL1Bridge.sol

10:    uint16 _l2TxNumberInBlock,
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/interfaces/IL1Bridge.sol

### Multiplication/division by a number thats a power of 2 should use bit shifting

`x * 2` is equivalent to `x << 1` and `x / 2` is the same as `x >> 1`. The `MUL` and `DIV` opcodes cost 5 gas, whereas `SHL` and `SHR` only cost 3 gas

_There are **4** instances of this issue:_

```solidity
File: ethereum/contracts/common/L2ContractHelper.sol

53:     require(bytecodeLenInWords < 2**16, "pp");

72:    codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol

```solidity
File: ethereum/contracts/zksync/Config.sol

23:   uint256 constant INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + 64 * 4896;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Config.sol

```solidity
File: ethereum/contracts/zksync/libraries/Merkle.sol

25:    require(_index < 2**pathLength, "pz");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Merkle.sol

### Splitting `require()` statements that use `&&` saves gas

Instead of using `&&` on single `require` check using two `require` checks can save gas

_There are **2** instances of this issue:_

```solidity
File: ethereum/contracts/common/AllowList.sol

96:    require(
97:        callersLength == _targets.length &&
98:            callersLength == _functionSigs.length &&
99:            callersLength == _enables.length,
100:      "yw"
101:   );
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol

```solidity
File: ethereum/contracts/common/L2ContractHelper.sol

65:     require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol

### Use `calldata` instead of `memory` for function parameters

If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory. Try to use calldata as a data location because it will avoid copies and also makes sure that the data cannot be modified.

_There are **26** instances of this issue:_

```solidity
File: ethereum/contracts/bridge/L1ERC20Bridge.sol

260:  function _parseL2WithdrawalMessage(bytes memory _l2ToL1message)
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol

```solidity
File: ethereum/contracts/bridge/L1EthBridge.sol

214:  function _parseL2WithdrawalMessage(bytes memory _message)
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol

```solidity
File: ethereum/contracts/common/L2ContractHelper.sol

43:   function sendMessageToL1(bytes memory _message) internal returns (bytes32) {

47:   function hashL2Bytecode(bytes memory _bytecode) internal pure returns (bytes32 hashedBytecode) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol

```solidity
File: ethereum/contracts/common/libraries/UnsafeBytes.sol

18:   function readUint32(bytes memory _bytes, uint256 _start) internal pure returns (uint32 result, uint256 offset) {

25:   function readAddress(bytes memory _bytes, uint256 _start) internal pure returns (address result, uint256 offset) {

32:   function readUint256(bytes memory _bytes, uint256 _start) internal pure returns (uint256 result, uint256 offset) {

39:   function readBytes32(bytes memory _bytes, uint256 _start) internal pure returns (bytes32 result, uint256 offset) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/libraries/UnsafeBytes.sol

```solidity
File: ethereum/contracts/zksync/facets/Executor.sol

23:   function _commitOneBlock(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)

80:   function _calculateBlockHash(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)

152:  function commitBlocks(StoredBlockInfo memory _lastCommittedBlockData, CommitBlockInfo[] calldata _newBlocksData)

190:  function _executeOneBlock(StoredBlockInfo memory _storedBlock, uint256 _executedBlockIdx) internal {

278:  VerifierParams memory _verifierParams

385:  function _hashStoredBlockInfo(StoredBlockInfo memory _storedBlockInfo) internal pure returns (bytes32) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol

```solidity
File: ethereum/contracts/zksync/facets/Mailbox.sol

43:    L2Log memory _log,

53:    L2Log memory _log,
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol

```solidity
File: ethereum/contracts/zksync/libraries/Diamond.sol

89:   function diamondCut(DiamondCutData memory _diamondCut) internal {

121:  bytes4[] memory _selectors,

145:  bytes4[] memory _selectors,

167:  function _removeFunctions(address _facet, bytes4[] memory _selectors) private {

277:  function _initializeDiamondCut(address _init, bytes memory _calldata) private {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol

```solidity
File: ethereum/contracts/zksync/libraries/PriorityQueue.sol

54:   function pushBack(Queue storage _queue, PriorityOperation memory _operation) internal {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol

```solidity
File: zksync/contracts/ExternalDecoder.sol

10:   function decodeString(bytes memory _input) external pure returns (string memory result) {

15:   function decodeUint8(bytes memory _input) external pure returns (uint8 result) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/ExternalDecoder.sol

```solidity
File: zksync/contracts/L2ContractHelper.sol

26:   function sendMessageToL1(bytes memory _message) internal returns (bytes32) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/L2ContractHelper.sol

```solidity
File: zksync/contracts/bridge/L2StandardERC20.sol

43:   function bridgeInitialize(address _l1Address, bytes memory _data) external initializer {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol

### Replace `x <= y` with `x < y + 1`, and `x >= y` with `x > y - 1`

In the EVM, there is no opcode for `>=` or `<=`. When using greater than or equal, two operations are performed: `>` and `=`. Using strict comparison operators hence saves gas

_There are **9** instances of this issue:_

```solidity
File: ethereum/contracts/zksync/DiamondProxy.sol

24:    require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondProxy.sol

```solidity
File: ethereum/contracts/zksync/facets/DiamondCut.sol

49:    bool approvedBySecurityCouncil = s.diamondCutStorage.securityCouncilEmergencyApprovals >=

52:    bool upgradeNoticePeriodPassed = block.timestamp >=
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol

```solidity
File: ethereum/contracts/zksync/facets/Executor.sol

50:   bool timestampNotTooSmall = block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= l2BlockTimestamp;

51:   bool timestampNotTooBig = l2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA;

216:  require(s.totalBlocksExecuted <= s.totalBlocksVerified, "n");

268:  require(currentTotalBlocksVerified <= s.totalBlocksCommitted, "q");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol

```solidity
File: ethereum/contracts/zksync/facets/Mailbox.sol

56:   require(_blockNumber <= s.totalBlocksExecuted, "xx");

124:  require(_ergsLimit <= PRIORITY_TX_MAX_ERGS_LIMIT, "ui");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol

### Use `immutable` & `constant` for state variables that do not change their value

_There are **10** instances of this issue:_

```solidity
File: ethereum/contracts/zksync/facets/Base.sol

12:   AppStorage internal s;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Base.sol

```solidity
File: zksync/contracts/bridge/L2ERC20Bridge.sol

19:   address public override l1Bridge;

23:   UpgradeableBeacon public l2TokenFactory;

26:   bytes32 l2TokenProxyBytecodeHash;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol

```solidity
File: zksync/contracts/bridge/L2ETHBridge.sol

16:   uint256 public totalSupply;

22:   address public override l1Bridge;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol

```solidity
File: zksync/contracts/bridge/L2StandardERC20.sol

24:   ERC20Getters availableGetters;

29:   uint8 private decimals_;

32:   address public override l2Bridge;

35:   address public override l1Address;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol

### Use custom errors rather than `revert()`/`require()` strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

_There are **98** instances of this issue:_

```solidity
File: ethereum/contracts/bridge/L1ERC20Bridge.sol

77:    require(_factoryDeps.length == 2, "mk");

117:   require(amount > 0, "1T");

207:  require(success, "yn");

210:  require(amount > 0, "y1");

232:  require(!isWithdrawalFinalized[_l2BlockNumber][_l2MessageIndex], "pw");

249:      require(success, "nq");

271:  require(_l2ToL1message.length == 76, "kk");

274:  require(bytes4(functionSignature) == this.finalizeWithdrawal.selector, "nt");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol

```solidity
File: ethereum/contracts/bridge/L1EthBridge.sol

93:   require(_l1Token == CONVENTIONAL_ETH_ADDRESS, "bx");

141:  require(_l1Token == CONVENTIONAL_ETH_ADDRESS, "sj");

166:  require(success, "ju");

189:  require(!isWithdrawalFinalized[_l2BlockNumber][_l2MessageIndex], "jj");

205:  require(success, "rj");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol

```solidity
File: ethereum/contracts/common/AllowList.sol

33:    require(_owner != address(0), "kq");

38:    require(msg.sender == owner, "kx");

67:    require(targetsLength == _enables.length, "yg");

96:    require(
97:        callersLength == _targets.length &&
98:            callersLength == _functionSigs.length &&
99:            callersLength == _enables.length,
100:      "yw"
101:   );

155:   require(msg.sender == newOwner, "n0");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol

```solidity
File: ethereum/contracts/common/AllowListed.sol

15:        require(_allowList.canCall(msg.sender, address(this), functionSig), "nr");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowListed.sol

```solidity
File: ethereum/contracts/common/L2ContractHelper.sol

50:    require(_bytecode.length % 32 == 0, "po");

53:     require(bytecodeLenInWords < 2**16, "pp");

54:     require(bytecodeLenInWords % 2 == 1, "pr");

65:     require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf");

67:     require(bytecodeLen(_bytecodeHash) % 2 == 1, "uy");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol

```solidity
File: ethereum/contracts/common/ReentrancyGuard.sol

55:    require(lockSlotOldValue == 0, "1B");

72:    require(_status == _NOT_ENTERED, "r1");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/ReentrancyGuard.sol

```solidity
File: ethereum/contracts/zksync/DiamondProxy.sol

13:    require(_chainId == block.chainid, "pr");

24:    require(msg.data.length >= 4 || msg.data.length == 0, "Ut");

29:     require(facetAddress != address(0), "F");

30:     require(!diamondStorage.isFrozen || !facet.isFreezable, "q1");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondProxy.sol

```solidity
File: ethereum/contracts/zksync/facets/Base.sol

16:     require(msg.sender == s.governor, "1g");

22:     require(s.validators[msg.sender], "1h");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Base.sol

```solidity
File: ethereum/contracts/zksync/facets/DiamondCut.sol

23:     require(s.diamondCutStorage.proposedDiamondCutTimestamp == 0, "a3");

40:     require(_resetProposal(), "g1");

55:     require(approvedBySecurityCouncil || upgradeNoticePeriodPassed, "a6");

56:    require(approvedBySecurityCouncil || !diamondStorage.isFrozen, "f3");

59:    require(
60:        s.diamondCutStorage.proposedDiamondCutHash ==
61:            keccak256(abi.encode(_diamondCut.facetCuts, _diamondCut.initAddress)),
62:        "a4"
63:     );

65:     require(_resetProposal(), "a5");

80:     require(!diamondStorage.isFrozen, "a9");

94:     require(diamondStorage.isFrozen, "a7");

106:   require(s.diamondCutStorage.securityCouncilMembers[msg.sender], "a9");

108:   require(s.diamondCutStorage.securityCouncilMemberLastApprovedProposalId[msg.sender] < currentProposalId, "ao");

111:   require(s.diamondCutStorage.proposedDiamondCutTimestamp != 0, "f0");

112:   require(s.diamondCutStorage.proposedDiamondCutHash == _diamondCutHash, "f1");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol

```solidity
File: ethereum/contracts/zksync/facets/Executor.sol

28:     require(_newBlock.blockNumber == _previousBlock.blockNumber + 1, "f");

39:    require(_previousBlock.blockHash == previousBlockHash, "l");

41:    require(expectedPriorityOperationsHash == _newBlock.priorityOperationsHash, "t");

52:         require(timestampNotTooSmall, "h");

53:         require(timestampNotTooBig, "h1");

57:        require(
58:            _previousBlock.indexRepeatedStorageChanges + newStorageChangesIndexes ==
59:                _newBlock.indexRepeatedStorageChanges,
60:            "yq"
61:        );

117:          require(keccak256(l2Messages[currentMessage]) == hashedMessage, "k2");

129:          require(!isSystemContextLogProcessed, "fx");

136:          require(bytecodeHash == L2ContractHelper.hashL2Bytecode(factoryDeps[currentBytecode]), "k3");

142:  require(currentBytecode == factoryDeps.length, "ym");

143:  require(currentMessage == l2Messages.length, "pl");

145:  require(isSystemContextLogProcessed, "by");

159:   require(s.storedBlockHashes[s.totalBlocksCommitted] == _hashStoredBlockInfo(_lastCommittedBlockData), "i");

192:   require(currentBlockNumber == s.totalBlocksExecuted + _executedBlockIdx + 1, "k");

193:  require(
194:      _hashStoredBlockInfo(_storedBlock) == s.storedBlockHashes[currentBlockNumber],
195:       "exe10"
196:  );

199:   require(priorityOperationsHash == _storedBlock.priorityOperationsHash, "x");

216:   require(s.totalBlocksExecuted <= s.totalBlocksVerified, "n");

237:  require(_hashStoredBlockInfo(_prevBlock) == s.storedBlockHashes[currentTotalBlocksVerified], "t1");

241:      require(
242:          _hashStoredBlockInfo(_committedBlocks[i]) ==
243:              s.storedBlockHashes[currentTotalBlocksVerified.uncheckedInc()],
244:          "o1"
245:      );

261:   require(successVerifyProof, "p");

265:   require(successProofAggregation, "hh");

268:   require(currentTotalBlocksVerified <= s.totalBlocksCommitted, "q");

337:   require(s.totalBlocksCommitted > _newLastBlock, "v1");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol

```solidity
File: ethereum/contracts/zksync/facets/Getters.sol

148:  require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Getters.sol

```solidity
File: ethereum/contracts/zksync/facets/Governance.sol

30:     require(msg.sender == pendingGovernor, "n4");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol

```solidity
File: ethereum/contracts/zksync/facets/Mailbox.sol

56:    require(_blockNumber <= s.totalBlocksExecuted, "xx");

63:    require(hashedLog != L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH, "tw");

66:    require(_proof.length == L2_TO_L1_LOG_MERKLE_TREE_HEIGHT, "rz");

124:   require(_ergsLimit <= PRIORITY_TX_MAX_ERGS_LIMIT, "ui");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol

```solidity
File: ethereum/contracts/zksync/libraries/Diamond.sol

100:       require(selectors.length > 0, "B");

109:           revert("C");

126:   require(_facet != address(0), "G");

135:       require(oldFacet.facetAddress == address(0), "J");

150:   require(_facet != address(0), "K");

156:       require(oldFacet.facetAddress != address(0), "L");

170:   require(_facet == address(0), "a1");

176:       require(oldFacet.facetAddress != address(0), "a2");

214:      require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");

279:       require(_calldata.length == 0, "H");

283:       require(success, "I");

287:      require(data.length == 32, "lp");

288:      require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol

```solidity
File: ethereum/contracts/zksync/libraries/Merkle.sol

23:    require(pathLength > 0, "xc");

24:    require(pathLength < 256, "bt");

25:    require(_index < 2**pathLength, "pz");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Merkle.sol

```solidity
File: ethereum/contracts/zksync/libraries/PriorityQueue.sol

64:     require(!_queue.isEmpty(), "D");

72:     require(!_queue.isEmpty(), "s");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol

```solidity
File: zksync/contracts/bridge/L2ERC20Bridge.sol

58:    require(msg.sender == l1Bridge, "mq");

63:        require(deployedToken == expectedL2Token, "mt");

95:    require(l1Token != address(0), "yh");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol

```solidity
File: zksync/contracts/bridge/L2ETHBridge.sol

49:    require(msg.sender == l1Bridge, "ni");

64:    require(_l2Token == CONVENTIONAL_ETH_ADDRESS, "zn");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol

```solidity
File: zksync/contracts/bridge/L2StandardERC20.sol

44:     require(l1Address == address(0), "in5");

45:     require(_l1Address != address(0), "in6");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol

### Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`

It is expected that the value should be converted into a constant value at compile time. But actually the expression is re-calculated each time the constant is referenced.

_There are **2** instances of this issue:_

```solidity
File: ethereum/contracts/common/L2ContractHelper.sol

41:   bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol

```solidity
File: zksync/contracts/L2ContractHelper.sol

24:   bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/L2ContractHelper.sol
