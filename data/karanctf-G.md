# Table of contents

- [1. Use bitwise `&` instead of mod with `2` as `forExample % 2 == 0` -> `forExample & 1`](#1-use-bitwise--instead-of-mod-with-2-as-forexample--2--0---forexample--1)
- [2. Use bitshift to calculate power of `2` with `1 << x` instead of `2**x`](#2-use-bitshift-to-calculate-power-of-2-with-1--x-instead-of-2x)
- [3. Multiple address mappings can be combined into a single mapping of an `address` to a `struct`, where appropriate](#3-multiple-address-mappings-can-be-combined-into-a-single-mapping-of-an-address-to-a-struct-where-appropriate)
- [4. Use default values insted of Explicit initialization with `0`.](#4-use-default-values-insted-of-explicit-initialization-with-0)
- [5. cache `.length` in loops](#5-cache-length-in-loops)
- [6. Use `!= 0` instead of `> 0`](#6-use--0-instead-of--0)
- [7. `<x> += <y>` costs more gas than `<x> = <x> + <y>`](#7-x--y-costs-more-gas-than-x--x--y)
- [8. Use `variable == false|0` -> `!variable` or `variable ==  true` -> `variable`](#8-use-variable--false0---variable-or-variable---true---variable)
- [9. Use Keccak256 over Sha256 for string comparation](#9-use-keccak256-over-sha256-for-string-comparation)
- [10.` >=` COSTS LESS GAS THAN `>`](#10--costs-less-gas-than-)
- [11. Use custom errors to save deployment and runtime costs in case of revert](#11-use-custom-errors-to-save-deployment-and-runtime-costs-in-case-of-revert)
- [12. `++i`/`i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops :](#12-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for--and-while-loops-)
- [13. Use `abi.encodePacked` for gas optimization](#13-use-abiencodepacked-for-gas-optimization)
- [14. Use `calldata` instead of `memory` on function parameters when used only for reading](#14-use-calldata-instead-of-memory-on-function-parameters-when-used-only-for-reading)

## 1. Use bitwise `&` instead of mod with `2` as `forExample % 2 == 0` -> `forExample & 1` 
```solidity
common/L2ContractHelper.sol:54:        require(bytecodeLenInWords % 2 == 1, "pr"); // bytecode length in words must be odd
common/L2ContractHelper.sol:67:        require(bytecodeLen(_bytecodeHash) % 2 == 1, "uy"); // Code length in words must be odd
zksync/libraries/Merkle.sol:29:            if (_index % 2 == 0) {
```

## 2. Use bitshift to calculate power of `2` with `1 << x` instead of `2**x`
```solidity
common/L2ContractHelper.sol:53:        require(bytecodeLenInWords < 2**16, "pp"); // bytecode length must be less than 2^16 words
zksync/libraries/Merkle.sol:25:        require(_index < 2**pathLength, "pz");
```
## 3. Multiple address mappings can be combined into a single mapping of an `address` to a `struct`, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.
```solidity
bridge/L1ERC20Bridge.sol:44:    mapping(address => mapping(address => mapping(bytes32 => uint256))) depositAmount;
common/AllowList.sol:30:    mapping(address => mapping(address => mapping(bytes4 => bool))) public hasSpecialAccessToCall;
```
## 4. Use default values insted of Explicit initialization with `0`.
It is not required for variable declaration of numTokens because `uints are 0` by default.removeing this will reduce contract size and save a bit of gas.

```solidity
common/AllowList.sol:69:        for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {
common/AllowList.sol:103:        for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
zksync/libraries/Merkle.sol:28:        for (uint256 i = 0; i < pathLength; i = i.uncheckedInc()) {
zksync/libraries/Diamond.sol:94:        for (uint256 i = 0; i < facetCutsLength; ++i) {
zksync/libraries/Diamond.sol:132:        for (uint256 i = 0; i < selectorsLength; ++i) {
zksync/libraries/Diamond.sol:153:        for (uint256 i = 0; i < selectorsLength; ++i) {
zksync/libraries/Diamond.sol:173:        for (uint256 i = 0; i < selectorsLength; ++i) {
zksync/facets/Getters.sol:163:        for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
zksync/facets/Executor.sol:111:        for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
zksync/facets/Executor.sol:162:        for (uint256 i = 0; i < blocksLength; i = i.uncheckedInc()) {
zksync/facets/Executor.sol:180:        for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {
zksync/facets/Executor.sol:210:        for (uint256 i = 0; i < nBlocks; i = i.uncheckedInc()) {
zksync/facets/Executor.sol:240:        for (uint256 i = 0; i < committedBlocksLength; i = i.uncheckedInc()) {
zksync/facets/Mailbox.sol:223:        for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
```

## 5. cache `.length` in loops
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.
Caching the array length in the stack saves around 3 gas per iteration.

```solidity
zksync/facets/Executor.sol:111:        for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
```
## 6. Use `!= 0` instead of `> 0`
```solidity
bridge/L1ERC20Bridge.sol:117:        require(amount > 0, "1T"); // empty deposit amount
bridge/L1ERC20Bridge.sol:210:        require(amount > 0, "y1");
zksync/libraries/Merkle.sol:23:        require(pathLength > 0, "xc");
zksync/libraries/Diamond.sol:100:            require(selectors.length > 0, "B"); // no functions for diamond cut
zksync/facets/DiamondCut.sol:16:        assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);
```

## 7. `<x> += <y>` costs more gas than `<x> = <x> + <y>` 
```solidity
zksync/libraries/Merkle.sol:34:            _index /= 2;
zksync/facets/DiamondCut.sol:29:        s.diamondCutStorage.currentProposalId += 1;
```
## 8. Use `variable == false|0` -> `!variable` or `variable ==  true` -> `variable`
```solidity
common/L2ContractHelper.sol:50:        require(_bytecode.length % 32 == 0, "po");
common/ReentrancyGuard.sol:55:        require(lockSlotOldValue == 0, "1B");
zksync/libraries/Merkle.sol:29:            if (_index % 2 == 0) {
zksync/libraries/Diamond.sol:189:        if (selectorsLength == 0) {
zksync/libraries/Diamond.sol:249:        if (lastSelectorPosition == 0) {
zksync/libraries/Diamond.sol:279:            require(_calldata.length == 0, "H"); // Non-empty calldata for zero address
zksync/DiamondProxy.sol:24:        require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
zksync/facets/Executor.sol:259:        // #if DUMMY_VERIFIER == false
zksync/facets/DiamondCut.sol:23:        require(s.diamondCutStorage.proposedDiamondCutTimestamp == 0, "a3"); // proposal already exists
zksync/facets/DiamondCut.sol:127:        if (s.diamondCutStorage.proposedDiamondCutTimestamp == 0) {
```
## 9. Use Keccak256 over Sha256 for string comparation
https://gist.github.com/alexon1234/5e8f4c335899a3398808bb96203bb982
```solidity
common/L2ContractHelper.sol:55:        hashedBytecode = sha256(_bytecode) & 0x00000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
```
## 10.` >=` COSTS LESS GAS THAN `>`
The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas https://gist.github.com/IllIllI000/3dc79d25acccfa16dee4e83ffdc6ffde 
```solidity
bridge/L1ERC20Bridge.sol:117:        require(amount > 0, "1T"); // empty deposit amount
bridge/L1ERC20Bridge.sol:210:        require(amount > 0, "y1");
zksync/libraries/Merkle.sol:23:        require(pathLength > 0, "xc");
zksync/libraries/Diamond.sol:100:            require(selectors.length > 0, "B"); // no functions for diamond cut
zksync/facets/Executor.sol:337:        require(s.totalBlocksCommitted > _newLastBlock, "v1"); // the last committed block is less new last block
zksync/facets/DiamondCut.sol:16:        assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);
```

## 11. Use custom errors to save deployment and runtime costs in case of revert
Instead of using strings for error messages (e.g., `require(msg.sender == owner, “unauthorized”)`), you can use custom errors to reduce both deployment and runtime gas costs. In addition, they are very convenient as you can easily pass dynamic information to them.
```solidity
bridge/L2StandardERC20.sol:44:        require(l1Address == address(0), "in5"); // Is already initialized
bridge/L2StandardERC20.sol:45:        require(_l1Address != address(0), "in6"); // Should be non-zero address
bridge/L2StandardERC20.sol:96:        require(msg.sender == l2Bridge);
bridge/L2ERC20Bridge.sol:58:        require(msg.sender == l1Bridge, "mq");
bridge/L2ERC20Bridge.sol:63:            require(deployedToken == expectedL2Token, "mt");
bridge/L2ERC20Bridge.sol:95:        require(l1Token != address(0), "yh");
bridge/L2ETHBridge.sol:49:        require(msg.sender == l1Bridge, "ni");
bridge/L2ETHBridge.sol:50:        require(_l1Token == CONVENTIONAL_ETH_ADDRESS);
bridge/L2ETHBridge.sol:64:        require(_l2Token == CONVENTIONAL_ETH_ADDRESS, "zn");
bridge/L1ERC20Bridge.sol:77:        require(_factoryDeps.length == 2, "mk");
bridge/L1ERC20Bridge.sol:117:        require(amount > 0, "1T"); // empty deposit amount
bridge/L1ERC20Bridge.sol:207:        require(success, "yn");
bridge/L1ERC20Bridge.sol:210:        require(amount > 0, "y1");
bridge/L1ERC20Bridge.sol:232:        require(!isWithdrawalFinalized[_l2BlockNumber][_l2MessageIndex], "pw");
bridge/L1ERC20Bridge.sol:249:            require(success, "nq");
bridge/L1ERC20Bridge.sol:271:        require(_l2ToL1message.length == 76, "kk");
bridge/L1ERC20Bridge.sol:274:        require(bytes4(functionSignature) == this.finalizeWithdrawal.selector, "nt");
bridge/L1EthBridge.sol:93:        require(_l1Token == CONVENTIONAL_ETH_ADDRESS, "bx");
bridge/L1EthBridge.sol:141:        require(_l1Token == CONVENTIONAL_ETH_ADDRESS, "sj");
bridge/L1EthBridge.sol:145:        require(amount != 0);
bridge/L1EthBridge.sol:166:        require(success, "ju");
bridge/L1EthBridge.sol:189:        require(!isWithdrawalFinalized[_l2BlockNumber][_l2MessageIndex], "jj");
bridge/L1EthBridge.sol:205:        require(success, "rj");
bridge/L1EthBridge.sol:221:        require(_message.length == 56);
bridge/L1EthBridge.sol:224:        require(bytes4(functionSignature) == this.finalizeWithdrawal.selector);
bridge/L1EthBridge.sol:238:        require(callSuccess);
common/AllowListed.sol:15:            require(_allowList.canCall(msg.sender, address(this), functionSig), "nr");
common/L2ContractHelper.sol:50:        require(_bytecode.length % 32 == 0, "po");
common/L2ContractHelper.sol:53:        require(bytecodeLenInWords < 2**16, "pp"); // bytecode length must be less than 2^16 words
common/L2ContractHelper.sol:54:        require(bytecodeLenInWords % 2 == 1, "pr"); // bytecode length in words must be odd
common/L2ContractHelper.sol:65:        require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
common/L2ContractHelper.sol:67:        require(bytecodeLen(_bytecodeHash) % 2 == 1, "uy"); // Code length in words must be odd
common/AllowList.sol:33:        require(_owner != address(0), "kq");
common/AllowList.sol:38:        require(msg.sender == owner, "kx");
common/AllowList.sol:67:        require(targetsLength == _enables.length, "yg"); // The size of arrays should be equal
common/AllowList.sol:96:        require(
common/AllowList.sol:155:        require(msg.sender == newOwner, "n0"); // Only proposed by current owner address can claim the owner rights
common/ReentrancyGuard.sol:55:        require(lockSlotOldValue == 0, "1B");
common/ReentrancyGuard.sol:72:        require(_status == _NOT_ENTERED, "r1");
zksync/libraries/PriorityQueue.sol:64:        require(!_queue.isEmpty(), "D"); // priority queue is empty
zksync/libraries/PriorityQueue.sol:72:        require(!_queue.isEmpty(), "s"); // priority queue is empty
zksync/libraries/Merkle.sol:23:        require(pathLength > 0, "xc");
zksync/libraries/Merkle.sol:24:        require(pathLength < 256, "bt");
zksync/libraries/Merkle.sol:25:        require(_index < 2**pathLength, "pz");
zksync/libraries/Diamond.sol:100:            require(selectors.length > 0, "B"); // no functions for diamond cut
zksync/libraries/Diamond.sol:126:        require(_facet != address(0), "G"); // facet with zero address cannot be added
zksync/libraries/Diamond.sol:135:            require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists
zksync/libraries/Diamond.sol:150:        require(_facet != address(0), "K"); // cannot replace facet with zero address
zksync/libraries/Diamond.sol:156:            require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address
zksync/libraries/Diamond.sol:170:        require(_facet == address(0), "a1"); // facet address must be zero
zksync/libraries/Diamond.sol:176:            require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet
zksync/libraries/Diamond.sol:214:            require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");
zksync/libraries/Diamond.sol:279:            require(_calldata.length == 0, "H"); // Non-empty calldata for zero address
zksync/libraries/Diamond.sol:283:            require(success, "I"); // delegatecall failed
zksync/libraries/Diamond.sol:287:            require(data.length == 32, "lp");
zksync/libraries/Diamond.sol:288:            require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");
zksync/DiamondProxy.sol:13:        require(_chainId == block.chainid, "pr");
zksync/DiamondProxy.sol:24:        require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
zksync/DiamondProxy.sol:29:        require(facetAddress != address(0), "F"); // Proxy has no facet for this selector
zksync/DiamondProxy.sol:30:        require(!diamondStorage.isFrozen || !facet.isFreezable, "q1"); // Facet is frozen
zksync/facets/Getters.sol:148:        require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");
zksync/facets/Executor.sol:28:        require(_newBlock.blockNumber == _previousBlock.blockNumber + 1, "f"); // only commit next block
zksync/facets/Executor.sol:39:        require(_previousBlock.blockHash == previousBlockHash, "l");
zksync/facets/Executor.sol:41:        require(expectedPriorityOperationsHash == _newBlock.priorityOperationsHash, "t");
zksync/facets/Executor.sol:43:        require(expectedNumberOfLayer1Txs == _newBlock.numberOfLayer1Txs);
zksync/facets/Executor.sol:45:        require(l2BlockTimestamp == _newBlock.timestamp);
zksync/facets/Executor.sol:52:            require(timestampNotTooSmall, "h"); // New block timestamp is too small
zksync/facets/Executor.sol:53:            require(timestampNotTooBig, "h1"); // New block timestamp is too big
zksync/facets/Executor.sol:57:            require(
zksync/facets/Executor.sol:117:                require(keccak256(l2Messages[currentMessage]) == hashedMessage, "k2");
zksync/facets/Executor.sol:129:                require(!isSystemContextLogProcessed, "fx");
zksync/facets/Executor.sol:136:                require(bytecodeHash == L2ContractHelper.hashL2Bytecode(factoryDeps[currentBytecode]), "k3");
zksync/facets/Executor.sol:142:        require(currentBytecode == factoryDeps.length, "ym");
zksync/facets/Executor.sol:143:        require(currentMessage == l2Messages.length, "pl");
zksync/facets/Executor.sol:145:        require(isSystemContextLogProcessed, "by");
zksync/facets/Executor.sol:159:        require(s.storedBlockHashes[s.totalBlocksCommitted] == _hashStoredBlockInfo(_lastCommittedBlockData), "i"); // incorrect previous block data
zksync/facets/Executor.sol:192:        require(currentBlockNumber == s.totalBlocksExecuted + _executedBlockIdx + 1, "k"); // Execute blocks in order
zksync/facets/Executor.sol:193:        require(
zksync/facets/Executor.sol:199:        require(priorityOperationsHash == _storedBlock.priorityOperationsHash, "x"); // priority operations hash does not match to expected
zksync/facets/Executor.sol:216:        require(s.totalBlocksExecuted <= s.totalBlocksVerified, "n"); // Can't execute blocks more then committed and proven currently.
zksync/facets/Executor.sol:237:        require(_hashStoredBlockInfo(_prevBlock) == s.storedBlockHashes[currentTotalBlocksVerified], "t1");
zksync/facets/Executor.sol:241:            require(
zksync/facets/Executor.sol:261:        require(successVerifyProof, "p"); // Proof verification fail
zksync/facets/Executor.sol:265:        require(successProofAggregation, "hh"); // Proof aggregation must be valid
zksync/facets/Executor.sol:268:        require(currentTotalBlocksVerified <= s.totalBlocksCommitted, "q");
zksync/facets/Executor.sol:297:        require(_recurisiveAggregationInput.length == 4);
zksync/facets/Executor.sol:337:        require(s.totalBlocksCommitted > _newLastBlock, "v1"); // the last committed block is less new last block
zksync/facets/Governance.sol:30:        require(msg.sender == pendingGovernor, "n4"); // Only proposed by current governor address can claim the governor rights
zksync/facets/Mailbox.sol:56:        require(_blockNumber <= s.totalBlocksExecuted, "xx");
zksync/facets/Mailbox.sol:63:        require(hashedLog != L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH, "tw");
zksync/facets/Mailbox.sol:66:        require(_proof.length == L2_TO_L1_LOG_MERKLE_TREE_HEIGHT, "rz");
zksync/facets/Mailbox.sol:124:        require(_ergsLimit <= PRIORITY_TX_MAX_ERGS_LIMIT, "ui");
zksync/facets/Base.sol:16:        require(msg.sender == s.governor, "1g"); // only by governor
zksync/facets/Base.sol:22:        require(s.validators[msg.sender], "1h"); // validator is not active
zksync/facets/DiamondCut.sol:23:        require(s.diamondCutStorage.proposedDiamondCutTimestamp == 0, "a3"); // proposal already exists
zksync/facets/DiamondCut.sol:40:        require(_resetProposal(), "g1"); // failed cancel diamond cut
zksync/facets/DiamondCut.sol:55:        require(approvedBySecurityCouncil || upgradeNoticePeriodPassed, "a6"); // notice period should expire
zksync/facets/DiamondCut.sol:56:        require(approvedBySecurityCouncil || !diamondStorage.isFrozen, "f3");
zksync/facets/DiamondCut.sol:59:        require(
zksync/facets/DiamondCut.sol:65:        require(_resetProposal(), "a5"); // failed reset proposal
zksync/facets/DiamondCut.sol:80:        require(!diamondStorage.isFrozen, "a9"); // diamond proxy is frozen already
zksync/facets/DiamondCut.sol:94:        require(diamondStorage.isFrozen, "a7"); // diamond proxy is not frozen
zksync/facets/DiamondCut.sol:106:        require(s.diamondCutStorage.securityCouncilMembers[msg.sender], "a9"); // not a security council member
zksync/facets/DiamondCut.sol:108:        require(s.diamondCutStorage.securityCouncilMemberLastApprovedProposalId[msg.sender] < currentProposalId, "ao"); // already approved this proposal
zksync/facets/DiamondCut.sol:111:        require(s.diamondCutStorage.proposedDiamondCutTimestamp != 0, "f0"); // there is no proposed diamond cut
zksync/facets/DiamondCut.sol:112:        require(s.diamondCutStorage.proposedDiamondCutHash == _diamondCutHash, "f1"); // proposed diamond cut do not match to the approved

```

## 12. `++i`/`i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops :
```solidity
zksync/libraries/Diamond.sol:94:        for (uint256 i = 0; i < facetCutsLength; ++i) {
zksync/libraries/Diamond.sol:132:        for (uint256 i = 0; i < selectorsLength; ++i) {
zksync/libraries/Diamond.sol:153:        for (uint256 i = 0; i < selectorsLength; ++i) {
zksync/libraries/Diamond.sol:173:        for (uint256 i = 0; i < selectorsLength; ++i) {
```
## 13. Use `abi.encodePacked` for gas optimization 
Changing the abi.encode function to abi.encodePacked  can save gas since the abi.encode function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, abi.encodePacked is more gas-efficient.
```solidity
bridge/L2ERC20Bridge.sol:114:        bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));
bridge/L1ERC20Bridge.sol:82:        bytes memory create2Input = abi.encode(address(this), l2ProxyTokenBytecodeHash, _governor);
bridge/L1ERC20Bridge.sol:168:        data = abi.encode(data1, data2, data3);
bridge/L1ERC20Bridge.sol:283:        bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));
bridge/L1EthBridge.sol:58:        bytes memory create2Input = abi.encode(address(this));
zksync/libraries/Merkle.sol:30:                currentHash = keccak256(abi.encode(currentHash, _path[i]));
zksync/libraries/Merkle.sol:32:                currentHash = keccak256(abi.encode(_path[i], currentHash));
zksync/facets/Executor.sol:85:        return keccak256(abi.encode(_previousBlock.blockHash, _newBlock.newStateRoot));
zksync/facets/Executor.sol:122:                chainedPriorityTxsHash = keccak256(abi.encode(chainedPriorityTxsHash, canonicalTxHash));
zksync/facets/Executor.sol:182:            concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));
zksync/facets/Executor.sol:359:        return keccak256(abi.encode(passThroughDataHash, metadataHash, auxiliaryOutputHash));
zksync/facets/Executor.sol:381:        return abi.encode(_block.l2LogsTreeRoot, l2ToL1LogsHash, initialStorageChangesHash, repeatedStorageChangesHash);
zksync/facets/Executor.sol:386:        return keccak256(abi.encode(_storedBlockInfo));
zksync/facets/Mailbox.sol:163:        canonicalTxHash = keccak256(abi.encode(transaction));
zksync/facets/DiamondCut.sol:27:        s.diamondCutStorage.proposedDiamondCutHash = keccak256(abi.encode(_facetCuts, _initAddress));
zksync/facets/DiamondCut.sol:61:                keccak256(abi.encode(_diamondCut.facetCuts, _diamondCut.initAddress)),
zksync/DiamondInit.sol:54:        s.storedBlockHashes[0] = keccak256(abi.encode(storedBlockZero));

```

## 14. Use `calldata` instead of `memory` on function parameters when used only for reading
```solidity
ExternalDecoder.sol:10:    function decodeString(bytes memory _input) external pure returns (string memory result) {
ExternalDecoder.sol:15:    function decodeUint8(bytes memory _input) external pure returns (uint8 result) {
L2ContractHelper.sol:6:    function sendToL1(bytes memory _message) external returns (bytes32);
L2ContractHelper.sol:26:    function sendMessageToL1(bytes memory _message) internal returns (bytes32) {
bridge/L2StandardERC20.sol:43:    function bridgeInitialize(address _l1Address, bytes memory _data) external initializer {
bridge/L1ERC20Bridge.sol:260:    function _parseL2WithdrawalMessage(bytes memory _l2ToL1message)
bridge/L1EthBridge.sol:214:    function _parseL2WithdrawalMessage(bytes memory _message)
common/libraries/UnsafeBytes.sol:18:    function readUint32(bytes memory _bytes, uint256 _start) internal pure returns (uint32 result, uint256 offset) {
common/libraries/UnsafeBytes.sol:25:    function readAddress(bytes memory _bytes, uint256 _start) internal pure returns (address result, uint256 offset) {
common/libraries/UnsafeBytes.sol:32:    function readUint256(bytes memory _bytes, uint256 _start) internal pure returns (uint256 result, uint256 offset) {
common/libraries/UnsafeBytes.sol:39:    function readBytes32(bytes memory _bytes, uint256 _start) internal pure returns (bytes32 result, uint256 offset) {
common/L2ContractHelper.sol:6:    function sendToL1(bytes memory _message) external returns (bytes32);
common/L2ContractHelper.sol:43:    function sendMessageToL1(bytes memory _message) internal returns (bytes32) {
common/L2ContractHelper.sol:47:    function hashL2Bytecode(bytes memory _bytecode) internal pure returns (bytes32 hashedBytecode) {
zksync/libraries/PriorityQueue.sol:54:    function pushBack(Queue storage _queue, PriorityOperation memory _operation) internal {
zksync/libraries/PriorityQueue.sol:71:    function popFront(Queue storage _queue) internal returns (PriorityOperation memory priorityOperation) {
zksync/libraries/Diamond.sol:89:    function diamondCut(DiamondCutData memory _diamondCut) internal {
zksync/libraries/Diamond.sol:167:    function _removeFunctions(address _facet, bytes4[] memory _selectors) private {
zksync/libraries/Diamond.sol:277:    function _initializeDiamondCut(address _init, bytes memory _calldata) private {
zksync/facets/Executor.sol:23:    function _commitOneBlock(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)
zksync/facets/Executor.sol:80:    function _calculateBlockHash(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)
zksync/facets/Executor.sol:152:    function commitBlocks(StoredBlockInfo memory _lastCommittedBlockData, CommitBlockInfo[] calldata _newBlocksData)
zksync/facets/Executor.sol:190:    function _executeOneBlock(StoredBlockInfo memory _storedBlock, uint256 _executedBlockIdx) internal {
zksync/facets/Executor.sol:385:    function _hashStoredBlockInfo(StoredBlockInfo memory _storedBlockInfo) internal pure returns (bytes32) {
zksync/interfaces/IGetters.sol:70:    function facetAddresses() external view returns (address[] memory facets);
```




