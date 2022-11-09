## [L-01] MISSING `address(0)` CHECK FOR CRITICAL ADDRESS INPUT
To prevent unintended behaviors, critical address input should be checked against `address(0)`. Please consider checking `_governor` in the following `initialize` function.

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#L25-L62
```solidity
    function initialize(
        Verifier _verifier,
        address _governor,
        address _validator,
        bytes32 _genesisBlockHash,
        uint64 _genesisIndexRepeatedStorageChanges,
        bytes32 _genesisBlockCommitment,
        IAllowList _allowList,
        VerifierParams calldata _verifierParams,
        bool _zkPorterIsAvailable,
        bytes32 _l2BootloaderBytecodeHash,
        bytes32 _l2DefaultAccountBytecodeHash
    ) external reentrancyGuardInitializer returns (bytes32) {
        s.verifier = _verifier;
        s.governor = _governor;
        ...
    }
```

## [L-02] UNRESOLVED TODO COMMENTS
Comments regarding todos indicate that there are unresolved action items for implementation, which need to be addressed before the protocol deployment. Please review the todo comments in the following code.

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L28-L29
```solidity
// TODO: change constant to the real root hash of empty Merkle tree (SMA-184)
bytes32 constant DEFAULT_L2_LOGS_TREE_ROOT_HASH = bytes32(0);
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L89-L96
```solidity
    function l2TransactionBaseCost(
        uint256, // _gasPrice
        uint256, // _ergsLimit
        uint32 // _calldataLength
    ) public pure returns (uint256) {
        // TODO: estimate gas for L1 execute
        return 0;
    }
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L127-L129
```solidity
        // TODO: Restore after stable priority op fee modeling. (SMA-1230)
        // uint256 baseCost = l2TransactionBaseCost(tx.gasprice, _ergsLimit, uint32(_calldata.length));
        // uint256 layer2Tip = msg.value - baseCost;
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L165-L171
```solidity
        s.priorityQueue.pushBack(
            PriorityOperation({
                canonicalTxHash: canonicalTxHash,
                expirationBlock: _expirationBlock,
                layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)
            })
        );
```

## [N-01] CONSTANTS CAN BE USED INSTEAD OF MAGIC NUMBERS
To improve readability and maintainability, constants can be used instead of magic numbers. Please consider replacing the following magic numbers, such as `56`, with constants.
```solidity
ethereum\contracts\bridge\L1EthBridge.sol
  221: require(_message.length == 56); 

ethereum\contracts\zksync\facets\Executor.sol
  100: bytes memory emittedL2Logs = _newBlock.l2Logs[4:];  
  112: (address logSender, ) = UnsafeBytes.readAddress(emittedL2Logs, i + 4);  
  116: (bytes32 hashedMessage, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + 56);  
  121: (bytes32 canonicalTxHash, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + 24);  
  130: (blockTimestamp, ) = UnsafeBytes.readUint256(emittedL2Logs, i + 24);  
  131: (previousBlockHash, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + 56);  
  135: (bytes32 bytecodeHash, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + 24);  

ethereum\contracts\zksync\libraries\Diamond.sol
  287: require(data.length == 32, "lp");
```

## [N-02] MISSING REASON STRINGS IN `require` STATEMENTS
When the reason strings are missing in the `require` statements, it is unclear about why certain conditions revert. Please add descriptive reason strings for the following `require` statements.

```solidity
ethereum\contracts\bridge\L1EthBridge.sol
  145: require(amount != 0);   
  224: require(bytes4(functionSignature) == this.finalizeWithdrawal.selector); 
  238: require(callSuccess);   

ethereum\contracts\zksync\facets\Executor.sol
  297: require(_recurisiveAggregationInput.length == 4);   

zksync\contracts\bridge\L2ETHBridge.sol
  50: require(_l1Token == CONVENTIONAL_ETH_ADDRESS);  
```

## [N-03] MISSING INDEXED EVENT FIELDS
Querying event can be optimized with index. Please consider adding `indexed` to the relevant fields of the following events.
```solidity
ethereum\contracts\zksync\interfaces\IDiamondCut.sol
  20: event DiamondCutProposal(Diamond.FacetCut[] _facetCuts, address _initAddress);

ethereum\contracts\zksync\libraries\Diamond.sol
  16: event DiamondCut(FacetCut[] facetCuts, address initAddress, bytes initCalldata);
```

## [N-04] MISSING NATSPEC COMMENTS
NatSpec comments provide rich code documentation. NatSpec comments are missing for the following functions. Please consider adding them.

```solidity
ethereum\contracts\common\L2ContractHelper.sol
  47: function hashL2Bytecode(bytes memory _bytecode) internal pure returns (bytes32 hashedBytecode) {    
  75: function computeCreate2Address( 

ethereum\contracts\common\ReentrancyGuard.sol
  43: function _initializeReentrancyGuard() private { 

ethereum\contracts\common\libraries\UncheckedMath.sol
  6: function uncheckedInc(uint256 _number) internal pure returns (uint256) { 
  12: function uncheckedAdd(uint256 _lhs, uint256 _rhs) internal pure returns (uint256) { 

ethereum\contracts\common\libraries\UnsafeBytes.sol
  18: function readUint32(bytes memory _bytes, uint256 _start) internal pure returns (uint32 result, uint256 offset) {    
  25: function readAddress(bytes memory _bytes, uint256 _start) internal pure returns (address result, uint256 offset) {    
  32: function readUint256(bytes memory _bytes, uint256 _start) internal pure returns (uint256 result, uint256 offset) {    
  39: function readBytes32(bytes memory _bytes, uint256 _start) internal pure returns (bytes32 result, uint256 offset) {    

ethereum\contracts\zksync\facets\Executor.sol
  362: function _blockPassThroughData(CommitBlockInfo calldata _block) internal pure returns (bytes memory) {  
  372: function _blockMetaParameters() internal view returns (bytes memory) {  
  376: function _blockAuxilaryOutput(CommitBlockInfo calldata _block) internal pure returns (bytes memory) {  

ethereum\contracts\zksync\facets\Mailbox.sol
  116: function _requestL2Transaction( 

zksync\contracts\L2ContractHelper.sol
  26: function sendMessageToL1(bytes memory _message) internal returns (bytes32) {
```

## [N-05] INCOMPLETE NATSPEC COMMENTS
NatSpec comments provide rich code documentation. @param and/or @return comments are missing for the following functions. Please consider completing NatSpec comments for them.

```solidity
ethereum\contracts\bridge\L1ERC20Bridge.sol
  136: function _depositFunds( 
  149: function _getDepositL2Calldata( 
  164: function _getERC20Getters(address _token) internal view returns (bytes memory data) { 
  260: function _parseL2WithdrawalMessage(bytes memory _l2ToL1message) 

ethereum\contracts\bridge\L1EthBridge.sol
  113: function _getDepositL2Calldata( 
  214: function _parseL2WithdrawalMessage(bytes memory _message)   
  232: function _withdrawFunds(address _to, uint256 _amount) internal {    

ethereum\contracts\common\L2ContractHelper.sol
  63: function validateBytecodeHash(bytes32 _bytecodeHash) internal pure {    
  71: function bytecodeLen(bytes32 _bytecodeHash) internal pure returns (uint256 codeLengthInWords) { 

ethereum\contracts\zksync\DiamondInit.sol
  25: function initialize(    

ethereum\contracts\zksync\facets\DiamondCut.sol
  46: function executeDiamondCutProposal(Diamond.DiamondCutData calldata _diamondCut) external onlyGovernor { 

ethereum\contracts\zksync\facets\Executor.sol
  23: function _commitOneBlock(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock) 
  89: function _processL2Logs(CommitBlockInfo calldata _newBlock) 
  152: function commitBlocks(StoredBlockInfo memory _lastCommittedBlockData, CommitBlockInfo[] calldata _newBlocksData)    
  177: function _collectOperationsFromPriorityQueue(uint256 _nPriorityOps) internal returns (bytes32 concatHash) { 
  190: function _executeOneBlock(StoredBlockInfo memory _storedBlock, uint256 _executedBlockIdx) internal {    
  208: function executeBlocks(StoredBlockInfo[] calldata _blocksData) external nonReentrant onlyValidator {    
  221: function proveBlocks(    
  274: function _getBlockProofPublicInput( 
  296: function _verifyRecursivePartOfProof(uint256[] calldata _recurisiveAggregationInput) internal view returns (bool) { 
  349: function _maxU256(uint256 a, uint256 b) internal pure returns (uint256) {   
  354: function _createBlockCommitment(CommitBlockInfo calldata _newBlockData) internal view returns (bytes32) {   
  385: function _hashStoredBlockInfo(StoredBlockInfo memory _storedBlockInfo) internal pure returns (bytes32) {    

ethereum\contracts\zksync\facets\Mailbox.sol
  144: function _writePriorityOp(  
  216: function _hashFactoryDeps(bytes[] calldata _factoryDeps)    

ethereum\contracts\zksync\libraries\Diamond.sol
  119: function _addFunctions( 
  143: function _replaceFunctions( 
  167: function _removeFunctions(address _facet, bytes4[] memory _selectors) private { 
  184: function _saveFacetIfNew(address _facet) private {  
  200: function _addOneFunction(   
  227: function _removeOneFunction(address _facet, bytes4 _selector) private { 
  256: function _removeFacet(address _facet) private { 
  277: function _initializeDiamondCut(address _init, bytes memory _calldata) private { 

ethereum\contracts\zksync\libraries\PriorityQueue.sol
  39: function getTotalPriorityTxs(Queue storage _queue) internal view returns (uint256) { 
  49: function isEmpty(Queue storage _queue) internal view returns (bool) { 
  54: function pushBack(Queue storage _queue, PriorityOperation memory _operation) internal { 
  71: function popFront(Queue storage _queue) internal returns (PriorityOperation memory priorityOperation) { 

zksync\contracts\bridge\L2ERC20Bridge.sol
  73: function _deployL2Token(address _l1Token, bytes calldata _data) internal returns (address) {    
  104: function _getL1WithdrawMessage( 
  113: function l2TokenAddress(address _l1Token) public view override returns (address) {   
  122: function _getCreate2Salt(address _l1Token) internal pure returns (bytes32 salt) {   

zksync\contracts\bridge\L2ETHBridge.sol
  59: function withdraw(  
  74: function _getL1WithdrawMessage(address _to, uint256 _amount) internal pure returns (bytes memory) {  
```