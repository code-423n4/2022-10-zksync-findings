### [G-01] Using default values is cheaper than assignment

If a variable is not set/initialized, it is assumed to have the default value 0 for uint, and false for boolean.
Explicitly initializing it with its default value is an anti-pattern and wastes gas.
For example: ```uint8 i = 0;``` should be replaced with ```uint8 i;```

I've found 14 locations in 6 files:

```
ethereum/contracts/common/AllowList.sol:
   68  
   69:         for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {
   70              _setPublicAccess(_targets[i], _enables[i]);

  102  
  103:         for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
  104              _setPermissionToCall(_callers[i], _targets[i], _functionSigs[i], _enables[i]);

ethereum/contracts/zksync/facets/Executor.sol:
  110          // linear traversal of the logs
  111:         for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
  112              (address logSender, ) = UnsafeBytes.readAddress(emittedL2Logs, i + 4);

  161          uint256 blocksLength = _newBlocksData.length;
  162:         for (uint256 i = 0; i < blocksLength; i = i.uncheckedInc()) {
  163              _lastCommittedBlockData = _commitOneBlock(_lastCommittedBlockData, _newBlocksData[i]);

  179  
  180:         for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {
  181              PriorityOperation memory priorityOp = s.priorityQueue.popFront();

  209          uint256 nBlocks = _blocksData.length;
  210:         for (uint256 i = 0; i < nBlocks; i = i.uncheckedInc()) {
  211              _executeOneBlock(_blocksData[i], i);

  239          bytes32 prevBlockCommitment = _prevBlock.commitment;
  240:         for (uint256 i = 0; i < committedBlocksLength; i = i.uncheckedInc()) {
  241              require(

ethereum/contracts/zksync/facets/Getters.sol:
  162  
  163:         for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
  164              address facetAddr = ds.facets[i];

ethereum/contracts/zksync/facets/Mailbox.sol:
  222          hashedFactoryDeps = new uint256[](factoryDepsLen);
  223:         for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
  224              bytes32 hashedBytecode = L2ContractHelper.hashL2Bytecode(_factoryDeps[i]);

ethereum/contracts/zksync/libraries/Diamond.sol:
   93          uint256 facetCutsLength = facetCuts.length;
   94:         for (uint256 i = 0; i < facetCutsLength; ++i) {
   95              Action action = facetCuts[i].action;

  131          uint256 selectorsLength = _selectors.length;
  132:         for (uint256 i = 0; i < selectorsLength; ++i) {
  133              bytes4 selector = _selectors[i];

  152          uint256 selectorsLength = _selectors.length;
  153:         for (uint256 i = 0; i < selectorsLength; ++i) {
  154              bytes4 selector = _selectors[i];

  172          uint256 selectorsLength = _selectors.length;
  173:         for (uint256 i = 0; i < selectorsLength; ++i) {
  174              bytes4 selector = _selectors[i];

ethereum/contracts/zksync/libraries/Merkle.sol:
  27          bytes32 currentHash = _itemHash;
  28:         for (uint256 i = 0; i < pathLength; i = i.uncheckedInc()) {
  29              if (_index % 2 == 0) {
```

---------------------------------------------------------------------------


### [G-02] != 0 is cheaper than > 0

!= 0 costs less gas compared to > 0 for unsigned integers even when optimizer enabled
All of the following findings are uint (E&OE) so >0 and != have exactly the same effect.
** saves 6 gas ** each

I've found 5 locations in 4 files:

```
ethereum/contracts/bridge/L1ERC20Bridge.sol:
  116          uint256 amount = _depositFunds(msg.sender, IERC20(_l1Token), _amount);
  117:         require(amount > 0, "1T"); // empty deposit amount
  118  

  209          uint256 amount = depositAmount[_depositSender][_l1Token][_l2TxHash];
  210:         require(amount > 0, "y1");
  211  

ethereum/contracts/zksync/facets/DiamondCut.sol:
  15          // Should be greater than 0, otherwise zero approvals will be enough to make an instant upgrade!
  16:         assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);
  17      }

ethereum/contracts/zksync/libraries/Diamond.sol:
   99  
  100:             require(selectors.length > 0, "B"); // no functions for diamond cut
  101  

ethereum/contracts/zksync/libraries/Merkle.sol:
  22          uint256 pathLength = _path.length;
  23:         require(pathLength > 0, "xc");
  24          require(pathLength < 256, "bt");

```

---------------------------------------------------------------------------

### [G-03] An arrays length should be cached to save gas in for-loops

An array’s length should be cached to save gas in for-loops
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset).
Caching the array length in the stack saves around **3 gas per iteration**.

I've found only 1 location!

```
ethereum/contracts/zksync/facets/Executor.sol:
  110          // linear traversal of the logs
  111:         for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
  112              (address logSender, ) = UnsafeBytes.readAddress(emittedL2Logs, i + 4);
```

---------------------------------------------------------------------------


### [G-04] Custom errors save gas

From solidity 0.8.4 and above (0.8.17 is used in 'zksync' solution)
Custom errors from are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)
Source: https://blog.soliditylang.org/2021/04/21/custom-errors/:
```Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.```

I've found 92 locations in 19 files:

```
ethereum/contracts/bridge/L1ERC20Bridge.sol:
   76          // We are expecting to see the exect two bytecodes that are needed to initiailize the bridge
   77:         require(_factoryDeps.length == 2, "mk");
   78          l2ProxyTokenBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[1]);

  116          uint256 amount = _depositFunds(msg.sender, IERC20(_l1Token), _amount);
  117:         require(amount > 0, "1T"); // empty deposit amount
  118  

  206          bool success = zkSyncMailbox.proveL2LogInclusion(_l2BlockNumber, _l2MessageIndex, l2Log, _merkleProof);
  207:         require(success, "yn");
  208  
  209          uint256 amount = depositAmount[_depositSender][_l1Token][_l2TxHash];
  210:         require(amount > 0, "y1");
  211  

  231      ) external nonReentrant senderCanCallFunction(allowList) {
  232:         require(!isWithdrawalFinalized[_l2BlockNumber][_l2MessageIndex], "pw");
  233  

  248              );
  249:             require(success, "nq");
  250          }

  270          // It should be equal to the length of the function signature + address + address + uint256 = 4 + 20 + 20 + 32 = 76 (bytes).
  271:         require(_l2ToL1message.length == 76, "kk");
  272  
  273          (uint32 functionSignature, uint256 offset) = UnsafeBytes.readUint32(_l2ToL1message, 0);
  274:         require(bytes4(functionSignature) == this.finalizeWithdrawal.selector, "nt");
  275  

ethereum/contracts/bridge/L1EthBridge.sol:
   92      ) external payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 txHash) {
   93:         require(_l1Token == CONVENTIONAL_ETH_ADDRESS, "bx");
   94  

  140      ) external override nonReentrant senderCanCallFunction(allowList) {
  141:         require(_l1Token == CONVENTIONAL_ETH_ADDRESS, "sj");
  142  

  165          bool success = zkSyncMailbox.proveL2LogInclusion(_l2BlockNumber, _l2MessageIndex, l2Log, _merkleProof);
  166:         require(success, "ju");
  167  

  188      ) external override nonReentrant senderCanCallFunction(allowList) {
  189:         require(!isWithdrawalFinalized[_l2BlockNumber][_l2MessageIndex], "jj");
  190  

  204          );
  205:         require(success, "rj");
  206  

ethereum/contracts/common/AllowList.sol:
   32      constructor(address _owner) {
   33:         require(_owner != address(0), "kq");
   34          owner = _owner;

   37      modifier onlyOwner() {
   38:         require(msg.sender == owner, "kx");
   39          _;

   66          uint256 targetsLength = _targets.length;
   67:         require(targetsLength == _enables.length, "yg"); // The size of arrays should be equal
   68  

  154          address newOwner = pendingOwner;
  155:         require(msg.sender == newOwner, "n0"); // Only proposed by current owner address can claim the owner rights
  156  

ethereum/contracts/common/AllowListed.sol:
  14              bytes4 functionSig = bytes4(msg.data[:4]);
  15:             require(_allowList.canCall(msg.sender, address(this), functionSig), "nr");
  16          }

ethereum/contracts/common/L2ContractHelper.sol:
  49          // must be provided in 32-byte words.
  50:         require(_bytecode.length % 32 == 0, "po");
  51  
  52          uint256 bytecodeLenInWords = _bytecode.length / 32;
  53:         require(bytecodeLenInWords < 2**16, "pp"); // bytecode length must be less than 2^16 words
  54:         require(bytecodeLenInWords % 2 == 1, "pr"); // bytecode length in words must be odd
  55          hashedBytecode = sha256(_bytecode) & 0x00000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;

  64          uint8 version = uint8(_bytecodeHash[0]);
  65:         require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
  66  
  67:         require(bytecodeLen(_bytecodeHash) % 2 == 1, "uy"); // Code length in words must be odd
  68      }

ethereum/contracts/common/ReentrancyGuard.sol:
  54          // Check that storage slot for reentrancy guard is empty to rule out possibility of slot conflict
  55:         require(lockSlotOldValue == 0, "1B");
  56      }

  71          // On the first call to nonReentrant, _notEntered will be true
  72:         require(_status == _NOT_ENTERED, "r1");
  73  

ethereum/contracts/zksync/DiamondProxy.sol:
  12          // Thus, the contract deployed by the same Create2 factory on the different chain will have different addresses!
  13:         require(_chainId == block.chainid, "pr");
  14          Diamond.diamondCut(_diamondCut);

  23          // which is not defined for data length in range [1, 3].
  24:         require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
  25          // Get facet from function selector

  28  
  29:         require(facetAddress != address(0), "F"); // Proxy has no facet for this selector
  30:         require(!diamondStorage.isFrozen || !facet.isFreezable, "q1"); // Facet is frozen
  31  

ethereum/contracts/zksync/facets/Base.sol:
  15      modifier onlyGovernor() {
  16:         require(msg.sender == s.governor, "1g"); // only by governor
  17          _;

  21      modifier onlyValidator() {
  22:         require(s.validators[msg.sender], "1h"); // validator is not active
  23          _;

ethereum/contracts/zksync/facets/DiamondCut.sol:
   22      function proposeDiamondCut(Diamond.FacetCut[] calldata _facetCuts, address _initAddress) external onlyGovernor {
   23:         require(s.diamondCutStorage.proposedDiamondCutTimestamp == 0, "a3"); // proposal already exists
   24  

   39          );
   40:         require(_resetProposal(), "g1"); // failed cancel diamond cut
   41      }

   54  
   55:         require(approvedBySecurityCouncil || upgradeNoticePeriodPassed, "a6"); // notice period should expire
   56:         require(approvedBySecurityCouncil || !diamondStorage.isFrozen, "f3");
   57          // should not be frozen or should have enough security council approvals

   64  
   65:         require(_resetProposal(), "a5"); // failed reset proposal
   66  

   79          Diamond.DiamondStorage storage diamondStorage = Diamond.getDiamondStorage();
   80:         require(!diamondStorage.isFrozen, "a9"); // diamond proxy is frozen already
   81          _resetProposal();

   93  
   94:         require(diamondStorage.isFrozen, "a7"); // diamond proxy is not frozen
   95  

  105      function approveEmergencyDiamondCutAsSecurityCouncilMember(bytes32 _diamondCutHash) external {
  106:         require(s.diamondCutStorage.securityCouncilMembers[msg.sender], "a9"); // not a security council member
  107          uint256 currentProposalId = s.diamondCutStorage.currentProposalId;
  108:         require(s.diamondCutStorage.securityCouncilMemberLastApprovedProposalId[msg.sender] < currentProposalId, "ao"); // already approved this proposal
  109          s.diamondCutStorage.securityCouncilMemberLastApprovedProposalId[msg.sender] = currentProposalId;
  110  
  111:         require(s.diamondCutStorage.proposedDiamondCutTimestamp != 0, "f0"); // there is no proposed diamond cut
  112:         require(s.diamondCutStorage.proposedDiamondCutHash == _diamondCutHash, "f1"); // proposed diamond cut do not match to the approved
  113          uint256 securityCouncilEmergencyApprovals = s.diamondCutStorage.securityCouncilEmergencyApprovals;

ethereum/contracts/zksync/facets/Executor.sol:
   27      {
   28:         require(_newBlock.blockNumber == _previousBlock.blockNumber + 1, "f"); // only commit next block
   29  

   38  
   39:         require(_previousBlock.blockHash == previousBlockHash, "l");
   40          // Check that the priority operation hash in the L2 logs is as expected
   41:         require(expectedPriorityOperationsHash == _newBlock.priorityOperationsHash, "t");
   42          // Check that the number of processed priority operations is as expected

   51              bool timestampNotTooBig = l2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA;
   52:             require(timestampNotTooSmall, "h"); // New block timestamp is too small
   53:             require(timestampNotTooBig, "h1"); // New block timestamp is too big
   54  

  116                  (bytes32 hashedMessage, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + 56);
  117:                 require(keccak256(l2Messages[currentMessage]) == hashedMessage, "k2");
  118  

  128                  // avoid accident double reading `blockTimestamp` and `previousBlockHash`
  129:                 require(!isSystemContextLogProcessed, "fx");
  130                  (blockTimestamp, ) = UnsafeBytes.readUint256(emittedL2Logs, i + 24);

  135                  (bytes32 bytecodeHash, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + 24);
  136:                 require(bytecodeHash == L2ContractHelper.hashL2Bytecode(factoryDeps[currentBytecode]), "k3");
  137  

  141          // To check that only relevant preimages have been included in the calldata
  142:         require(currentBytecode == factoryDeps.length, "ym");
  143:         require(currentMessage == l2Messages.length, "pl");
  144          // `blockTimestamp` and `previousBlockHash` wasn't read from L2 logs
  145:         require(isSystemContextLogProcessed, "by");
  146      }

  158          // Check that we commit blocks after last committed block
  159:         require(s.storedBlockHashes[s.totalBlocksCommitted] == _hashStoredBlockInfo(_lastCommittedBlockData), "i"); // incorrect previous block data
  160  

  191          uint256 currentBlockNumber = _storedBlock.blockNumber;
  192:         require(currentBlockNumber == s.totalBlocksExecuted + _executedBlockIdx + 1, "k"); // Execute blocks in order
  193          require(

  198          bytes32 priorityOperationsHash = _collectOperationsFromPriorityQueue(_storedBlock.numberOfLayer1Txs);
  199:         require(priorityOperationsHash == _storedBlock.priorityOperationsHash, "x"); // priority operations hash does not match to expected
  200  

  215          s.totalBlocksExecuted = s.totalBlocksExecuted + nBlocks;
  216:         require(s.totalBlocksExecuted <= s.totalBlocksVerified, "n"); // Can't execute blocks more then committed and proven currently.
  217      }

  236          // Check that the block passed by the validator is indeed the first unverified block
  237:         require(_hashStoredBlockInfo(_prevBlock) == s.storedBlockHashes[currentTotalBlocksVerified], "t1");
  238  

  260          bool successVerifyProof = s.verifier.verify_serialized_proof(proofPublicInput, _proof.serializedProof);
  261:         require(successVerifyProof, "p"); // Proof verification fail
  262  

  264          bool successProofAggregation = _verifyRecursivePartOfProof(_proof.recurisiveAggregationInput);
  265:         require(successProofAggregation, "hh"); // Proof aggregation must be valid
  266          // #endif
  267  
  268:         require(currentTotalBlocksVerified <= s.totalBlocksCommitted, "q");
  269          emit BlocksVerification(s.totalBlocksVerified, currentTotalBlocksVerified);

  336      function revertBlocks(uint256 _newLastBlock) external nonReentrant onlyValidator {
  337:         require(s.totalBlocksCommitted > _newLastBlock, "v1"); // the last committed block is less new last block
  338          uint256 newTotalBlocksCommitted = _maxU256(_newLastBlock, s.totalBlocksExecuted);

ethereum/contracts/zksync/facets/Getters.sol:
  147          Diamond.DiamondStorage storage ds = Diamond.getDiamondStorage();
  148:         require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");
  149          return ds.selectorToFacet[_selector].isFreezable;

ethereum/contracts/zksync/facets/Governance.sol:
  29          address pendingGovernor = s.pendingGovernor;
  30:         require(msg.sender == pendingGovernor, "n4"); // Only proposed by current governor address can claim the governor rights
  31  

ethereum/contracts/zksync/facets/Mailbox.sol:
   55      ) internal view returns (bool) {
   56:         require(_blockNumber <= s.totalBlocksExecuted, "xx");
   57  

   62          // otherwise it means that the value is out of range of sent L2 -> L1 logs
   63:         require(hashedLog != L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH, "tw");
   64          // Check that the proof length is exactly the same as tree height, to prevent
   65          // any shorter/longer paths attack on the Merkle path validation
   66:         require(_proof.length == L2_TO_L1_LOG_MERKLE_TREE_HEIGHT, "rz");
   67  

  123      ) internal returns (bytes32 canonicalTxHash) {
  124:         require(_ergsLimit <= PRIORITY_TX_MAX_ERGS_LIMIT, "ui");
  125          uint64 expirationBlock = uint64(block.number + PRIORITY_EXPIRATION);

ethereum/contracts/zksync/libraries/Diamond.sol:
   99  
  100:             require(selectors.length > 0, "B"); // no functions for diamond cut
  101  

  125  
  126:         require(_facet != address(0), "G"); // facet with zero address cannot be added
  127  

  134              SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
  135:             require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists
  136  

  149  
  150:         require(_facet != address(0), "K"); // cannot replace facet with zero address
  151  

  155              SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
  156:             require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address
  157  

  169  
  170:         require(_facet == address(0), "a1"); // facet address must be zero
  171  

  175              SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
  176:             require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet
  177  

  213              bytes4 selector0 = ds.facetToSelectors[_facet].selectors[0];
  214:             require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");
  215          }

  278          if (_init == address(0)) {
  279:             require(_calldata.length == 0, "H"); // Non-empty calldata for zero address
  280          } else {

  282              (bool success, bytes memory data) = _init.delegatecall(_calldata);
  283:             require(success, "I"); // delegatecall failed
  284  

  286              // supposed to be used as diamond cut initializer.
  287:             require(data.length == 32, "lp");
  288:             require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");
  289          }

ethereum/contracts/zksync/libraries/Merkle.sol:
  22          uint256 pathLength = _path.length;
  23:         require(pathLength > 0, "xc");
  24:         require(pathLength < 256, "bt");
  25:         require(_index < 2**pathLength, "pz");
  26  

ethereum/contracts/zksync/libraries/PriorityQueue.sol:
  63      function front(Queue storage _queue) internal view returns (PriorityOperation memory) {
  64:         require(!_queue.isEmpty(), "D"); // priority queue is empty
  65  

  71      function popFront(Queue storage _queue) internal returns (PriorityOperation memory priorityOperation) {
  72:         require(!_queue.isEmpty(), "s"); // priority queue is empty
  73  

zksync/contracts/bridge/L2ERC20Bridge.sol:
  57          // Only L1 bridge counterpart can initiate and finalize the deposit
  58:         require(msg.sender == l1Bridge, "mq");
  59  

  62              address deployedToken = _deployL2Token(_l1Token, _data);
  63:             require(deployedToken == expectedL2Token, "mt");
  64              l1TokenAddress[expectedL2Token] = _l1Token;

  94          address l1Token = l1TokenAddress[_l2Token];
  95:         require(l1Token != address(0), "yh");
  96  

zksync/contracts/bridge/L2ETHBridge.sol:
  48          // Only L1 bridge counterpart can initiate and finalize the deposit
  49:         require(msg.sender == l1Bridge, "ni");
  50          require(_l1Token == CONVENTIONAL_ETH_ADDRESS);

  63      ) external override {
  64:         require(_l2Token == CONVENTIONAL_ETH_ADDRESS, "zn");
  65  

zksync/contracts/bridge/L2StandardERC20.sol:
  43      function bridgeInitialize(address _l1Address, bytes memory _data) external initializer {
  44:         require(l1Address == address(0), "in5"); // Is already initialized
  45:         require(_l1Address != address(0), "in6"); // Should be non-zero address
  46          l1Address = _l1Address;

```