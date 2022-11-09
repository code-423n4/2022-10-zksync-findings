## FINDINGS
NB: *Some functions have been truncated where neccessary to just show affected parts of the code*
Throught the report some places might be denoted with audit tags to show the actual place affected.

### Pack structs by putting data types that can be packed together next to each other.

As the solidity EVM works with 32 bytes, variables less than 32 bytes should be packed inside a struct so that they can be stored in the same slot, this saves gas when writing to storage ~20000 gas

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Storage.sol#L69-L105
**Save 1 SLOT**
```solidity
File:/ethereum/contracts/zksync/Storage.sol
struct AppStorage {
    /// @dev Storage of variables needed for diamond cut facet
    DiamondCutStorage diamondCutStorage;
    /// @notice Address which will exercise governance over the network i.e. change validator set, conduct upgrades
    address governor;
    /// @notice Address that governor proposed as one that will replace it
    address pendingGovernor;
    /// @notice List of permitted validators
    mapping(address => bool) validators;
    /// @dev Verifier contract. Used to verify aggregated proof for blocks
    Verifier verifier;
    /// @notice Total number of executed blocks i.e. blocks[totalBlocksExecuted] points at the latest executed block (block 0 is genesis)
    uint256 totalBlocksExecuted;
    /// @notice Total number of proved blocks i.e. blocks[totalBlocksProved] points at the latest proved block
    uint256 totalBlocksVerified;
    /// @notice Total number of committed blocks i.e. blocks[totalBlocksCommitted] points at the latest committed block
    uint256 totalBlocksCommitted;
    /// @dev Stored hashed StoredBlock for block number
    mapping(uint256 => bytes32) storedBlockHashes;
    /// @dev Stored root hashes of L2 -> L1 logs
    mapping(uint256 => bytes32) l2LogsRootHashes;
    /// @dev Container that stores transactions requested from L1
    PriorityQueue.Queue priorityQueue;
    /// @dev The smart contract that manages the list with permission to call contract functions
    IAllowList allowList;
    /// @notice Part of the configuration parameters of ZKP circuits. Used as an input for the verifier smart contract
    VerifierParams verifierParams;
    /// @notice Bytecode hash of bootloader program.
    /// @dev Used as an input to zkp-circuit.
    bytes32 l2BootloaderBytecodeHash;
    /// @notice Bytecode hash of default account (bytecode for EOA).
    /// @dev Used as an input to zkp-circuit.
    bytes32 l2DefaultAccountBytecodeHash;
    /// @dev Indicates that the porter may be touched on L2 transactions.
    /// @dev Used as an input to zkp-circuit.
    bool zkPorterIsAvailable;
}
```

```diff
diff --git a/ethereum/contracts/zksync/Storage.sol b/ethereum/contracts/zksync/Storage.sol
index c88337e..84b63a4 100644
--- a/ethereum/contracts/zksync/Storage.sol
+++ b/ethereum/contracts/zksync/Storage.sol
@@ -69,6 +69,7 @@ struct VerifierParams {
 struct AppStorage {
     /// @dev Storage of variables needed for diamond cut facet
     DiamondCutStorage diamondCutStorage;
+    bool zkPorterIsAvailable;
     /// @notice Address which will exercise governance over the network i.e. change validator set, conduct upgrades
     address governor;
     /// @notice Address that governor proposed as one that will replace it
@@ -101,5 +102,5 @@ struct AppStorage {
     bytes32 l2DefaultAccountBytecodeHash;
     /// @dev Indicates that the porter may be touched on L2 transactions.
     /// @dev Used as an input to zkp-circuit.
-    bool zkPorterIsAvailable;
+
 }
```


https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IExecutor.sol#L15-L24
### IExecutor.sol.StoredBlockInfo{}: From 7 slots to 6 slots
```solidity
File: /ethereum/contracts/zksync/interfaces/IExecutor.sol

//@audit: move indexRepeatedStorageChanges next to blockNumber as they can be packed together
15:    struct StoredBlockInfo {
16:        uint64 blockNumber;
17:        bytes32 blockHash;
18:        uint64 indexRepeatedStorageChanges;
19:        uint256 numberOfLayer1Txs;
20:        bytes32 priorityOperationsHash;
21:        bytes32 l2LogsTreeRoot;
22:        uint256 timestamp;
23:        bytes32 commitment;
24:    }
```

```diff
diff --git a/ethereum/contracts/zksync/interfaces/IExecutor.sol b/ethereum/contracts/zksync/interfaces/IExecutor.sol
index 1144bca..a901147 100644
--- a/ethereum/contracts/zksync/interfaces/IExecutor.sol
+++ b/ethereum/contracts/zksync/interfaces/IExecutor.sol
@@ -14,8 +14,8 @@ interface IExecutor {
     /// @param commitment Verified input for the zkSync circuit
     struct StoredBlockInfo {
         uint64 blockNumber;
-        bytes32 blockHash;
         uint64 indexRepeatedStorageChanges;
+        bytes32 blockHash;
         uint256 numberOfLayer1Txs;
         bytes32 priorityOperationsHash;
         bytes32 l2LogsTreeRoot;
```


### Cache storage values in memory to minimize SLOADs
The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Governance.sol#L28-L40
### Governance.sol.acceptGovernor(): s.governor should be cached
```solidity
File:/ethereum/contracts/zksync/facets/Governance.sol
28:    function acceptGovernor() external {

32:        if (pendingGovernor != s.governor) { //@audit: 1st SLOAD
33:            address previousGovernor = s.governor; //@audit: 2nd SLOAD
34:            s.governor = pendingGovernor;
35:            delete s.pendingGovernor;
            
40:    }
```

### Internal/Private functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

Affected code:

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L23-L27
```solidity
File: /ethereum/contracts/zksync/facets/Executor.sol
23:    function _commitOneBlock(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)
24:        internal
25:        view
26:        returns (StoredBlockInfo memory storedNewBlock)
27:    {
```

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L89-L90
```solidity
File: /ethereum/contracts/zksync/facets/Executor.sol
89:    function _processL2Logs(CommitBlockInfo calldata _newBlock)
90:        internal
```

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L177
```solidity
File:/ethereum/contracts/zksync/facets/Executor.sol
177:    function _collectOperationsFromPriorityQueue(uint256 _nPriorityOps) internal returns (bytes32 concatHash) {
```

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L190
```solidity
File: /ethereum/contracts/zksync/facets/Executor.sol
190:    function _executeOneBlock(StoredBlockInfo memory _storedBlock, uint256 _executedBlockIdx) internal {
```

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L274-L279
```solidity
File: /ethereum/contracts/zksync/facets/Executor.sol
274:    function _getBlockProofPublicInput(
275:        bytes32 _prevBlockCommitment,
276:        bytes32 _currentBlockCommitment,
277:        ProofInput calldata _proof,
278:        VerifierParams memory _verifierParams
279:    ) internal pure returns (uint256) {
```

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L296
```solidity
File: /ethereum/contracts/zksync/facets/Executor.sol
296:    function _verifyRecursivePartOfProof(uint256[] calldata _recurisiveAggregationInput) internal view returns (bool) {
```

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L349
```solidity
File: /ethereum/contracts/zksync/facets/Executor.sol
349:    function _maxU256(uint256 a, uint256 b) internal pure returns (uint256) {
```

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L354
```solidity
File: /ethereum/contracts/zksync/facets/Executor.sol
354:    function _createBlockCommitment(CommitBlockInfo calldata _newBlockData) internal view returns (bytes32) {
```
https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L362
```solidity
File: /ethereum/contracts/zksync/facets/Executor.sol
362:    function _blockPassThroughData(CommitBlockInfo calldata _block) internal pure returns (bytes memory) {
```

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L372
```solidity
File: /ethereum/contracts/zksync/facets/Executor.sol
372:    function _blockMetaParameters() internal view returns (bytes memory) {
```

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L376
```solidity
File:/ethereum/contracts/zksync/facets/Executor.sol
376:    function _blockAuxilaryOutput(CommitBlockInfo calldata _block) internal pure returns (bytes memory) {
```
https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L75
```solidity
File: /ethereum/contracts/zksync/facets/Mailbox.sol
75:    function _L2MessageToLog(L2Message calldata _message) internal pure returns (L2Log memory) {
```

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L116-L123
```solidity
File: /ethereum/contracts/zksync/facets/Mailbox.sol
116:    function _requestL2Transaction(
117:        address _sender,
118:        address _contractL2,
119:        uint256 _l2Value,
120:        bytes calldata _calldata,
121:        uint256 _ergsLimit,
122:        bytes[] calldata _factoryDeps
123:    ) internal returns (bytes32 canonicalTxHash) {
```

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L144-L153
```solidity
File: /ethereum/contracts/zksync/facets/Mailbox.sol
144:    function _writePriorityOp(
145:        address _sender,
146:        uint256 _txId,
147:        uint256 _l2Value,
148:        address _contractAddressL2,
149:        bytes calldata _calldata,
150:        uint64 _expirationBlock,
151:        uint256 _ergsLimit,
152:        bytes[] calldata _factoryDeps
153:    ) internal returns (bytes32 canonicalTxHash) {
```

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L216-L220
```solidity
File: /ethereum/contracts/zksync/facets/Mailbox.sol
216:    function _hashFactoryDeps(bytes[] calldata _factoryDeps)
217:        internal
218:        pure
219:        returns (uint256[] memory hashedFactoryDeps)
220:    {
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L136-L140
```solidity
File: /ethereum/contracts/bridge/L1ERC20Bridge.sol
136:    function _depositFunds(
137:        address _from,
138:        IERC20 _token,
139:        uint256 _amount
140:    ) internal returns (uint256) {


149:    function _getDepositL2Calldata(
150:        address _l1Sender,
151:        address _l2Receiver,
152:        address _l1Token,
153:        uint256 _amount
154:    ) internal view returns (bytes memory txCalldata) {


164:    function _getERC20Getters(address _token) internal view returns (bytes memory data) {

260:    function _parseL2WithdrawalMessage(bytes memory _l2ToL1message)
261:        internal

```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L113-L122
```solidity
File: /ethereum/contracts/bridge/L1EthBridge.sol
113:    function _getDepositL2Calldata(
114:        address _l1Sender,
115:        address _l2Receiver,
116:        uint256 _amount
117:    ) internal pure returns (bytes memory txCalldata) {


214:    function _parseL2WithdrawalMessage(bytes memory _message)
215:        internal
216:        pure
217:        returns (address l1Receiver, uint256 amount)
218:    {
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ERC20Bridge.sol#L73
```solidity
File: /zksync/contracts/bridge/L2ERC20Bridge.sol
73:    function _deployL2Token(address _l1Token, bytes calldata _data) internal returns (address) {
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ETHBridge.sol#L74
```solidity
File: /zksync/contracts/bridge/L2ETHBridge.sol
74:    function _getL1WithdrawMessage(address _to, uint256 _amount) internal pure returns (bytes memory) {
```
### Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time.
**Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it**

To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. 
As an example, instead of repeatedly calling ```someMap[someIndex]```, save its reference like this: ```SomeStruct storage someStruct = someMap[someIndex]``` and use it.


https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Governance.sol#L45-L50
### Governance.sol.setValidator(): s.validators[\_validator] should be cached in local storage
```solidity
File: /ethereum/contracts/zksync/facets/Governance.sol
45:    function setValidator(address _validator, bool _active) external onlyGovernor {
46:        if (s.validators[_validator] != _active) {
47:            s.validators[_validator] = _active;
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L225-L257
### L1ERC20Bridge.sol.finalizeWithdrawal(): isWithdrawalFinalized[\_l2BlockNumber][\_l2MessageIndex] should be cached
```solidity
File: /ethereum/contracts/bridge/L1ERC20Bridge.sol
225:    function finalizeWithdrawal(
    
232:        require(!isWithdrawalFinalized[_l2BlockNumber][_l2MessageIndex], "pw");

252:        isWithdrawalFinalized[_l2BlockNumber][_l2MessageIndex] = true;

257:    }
```
### Use calldata instead of memory for function parameters
If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.

Note that I've also flagged instances where the function is public but can be marked as external since it's not called by the contract, and cases where a constructor is involved

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L40-L47
```solidity
File: /ethereum/contracts/zksync/facets/Mailbox.sol

//@audit: use calldata on _log
40:    function proveL2LogInclusion(
41:        uint256 _blockNumber,
42:        uint256 _index,
43:        L2Log memory _log,
44:        bytes32[] calldata _proof
45:    ) external view returns (bool) {
46:        return _proveL2LogInclusion(_blockNumber, _index, _log, _proof);
47:    }
```

### Avoid contract existence checks by using solidity version 0.8.10 or later

Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L141
```solidity
File: /ethereum/contracts/bridge/L1ERC20Bridge.sol
141:        uint256 balanceBefore = _token.balanceOf(address(this));

143:        uint256 balanceAfter = _token.balanceOf(address(this));

206:        bool success = zkSyncMailbox.proveL2LogInclusion(_l2BlockNumber, _l2MessageIndex, l2Log, _merkleProof);

243:            bool success = zkSyncMailbox.proveL2MessageInclusion(
244:                _l2BlockNumber,
245:                _l2MessageIndex,
246:                l2ToL1Message,
247:                _merkleProof
248:            );
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L98-L104
```solidity
File: /ethereum/contracts/bridge/L1EthBridge.sol
98:        txHash = zkSyncMailbox.requestL2Transaction{value: zkSyncFee}(
99:            l2Bridge,
100:            0, // L2 msg.value
101:            l2TxCalldata,
102:            DEPOSIT_ERGS_LIMIT,
103:            new bytes[](0)
104:        );


165:        bool success = zkSyncMailbox.proveL2LogInclusion(_l2BlockNumber, _l2MessageIndex, l2Log, _merkleProof);


199:        bool success = zkSyncMailbox.proveL2MessageInclusion(
200:            _l2BlockNumber,
201:            _l2MessageIndex,
202:            l2ToL1Message,
203:            _merkleProof
204:        );
```

### Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L179-L187
```solidity
File:/ethereum/contracts/bridge/L1ERC20Bridge.sol

//@audit: uint16 _l2TxNumberInBlock
179:    function claimFailedDeposit(
189:        address _depositSender,
190:        address _l1Token,
191:        bytes32 _l2TxHash,
192:        uint256 _l2BlockNumber,
193:        uint256 _l2MessageIndex,
194:        uint16 _l2TxNumberInBlock,
195:        bytes32[] calldata _merkleProof
196:    ) external nonReentrant senderCanCallFunction(allowList) {


//@audit:  uint16 _l2TxNumberInBlock
225:    function finalizeWithdrawal(
226:        uint256 _l2BlockNumber,
227:        uint256 _l2MessageIndex,
228:        uint16 _l2TxNumberInBlock,
229:        bytes calldata _message,
230:        bytes32[] calldata _merkleProof
231:    ) external nonReentrant senderCanCallFunction(allowList) {
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L132-L140
```solidity
File: /ethereum/contracts/bridge/L1EthBridge.sol

//@audit: uint16 _l2TxNumberInBlock,
132:    function claimFailedDeposit(
133:        address _depositSender,
134:        address _l1Token,
135:        bytes32 _l2TxHash,
136:        uint256 _l2BlockNumber,
137:        uint256 _l2MessageIndex,
138:        uint16 _l2TxNumberInBlock,
139:        bytes32[] calldata _merkleProof
140:    ) external override nonReentrant senderCanCallFunction(allowList) {


//@audit: uint16 _l2TxNumberInBlock
182:    function finalizeWithdrawal(
183:        uint256 _l2BlockNumber,
184:        uint256 _l2MessageIndex,
185:        uint16 _l2TxNumberInBlock,
186:        bytes calldata _message,
187:        bytes32[] calldata _merkleProof
188:    ) external override nonReentrant senderCanCallFunction(allowList) {
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/AllowList.sol#L46-L50
```solidity
File: /ethereum/contracts/common/AllowList.sol

//@audit: bytes4 _functionSig
46:    function canCall(
47:        address _caller,
48:        address _target,
49:        bytes4 _functionSig
50:    ) external view returns (bool) {


//@audit: bytes4 _functionSig
113:    function setPermissionToCall(
114:        address _caller,
115:        address _target,
116:        bytes4 _functionSig,
117:        bool _enable
118:    ) external onlyOwner {

//@audit: bytes4 _functionSig
123:    function _setPermissionToCall(
124:        address _caller,
125:        address _target,
126:        bytes4 _functionSig,
127:        bool _enable
128:    ) internal {
```


https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/DiamondInit.sol#L25-L37
```solidity
File: /ethereum/contracts/zksync/DiamondInit.sol

//@audit: uint64 _genesisIndexRepeatedStorageChanges
25:    function initialize(
26:        Verifier _verifier,
27:        address _governor,
28:        address _validator,
29:        bytes32 _genesisBlockHash,
30:        uint64 _genesisIndexRepeatedStorageChanges,
31:        bytes32 _genesisBlockCommitment,
32:        IAllowList _allowList,
33:        VerifierParams calldata _verifierParams,
34:        bool _zkPorterIsAvailable,
35:        bytes32 _l2BootloaderBytecodeHash,
36:        bytes32 _l2DefaultAccountBytecodeHash
37:    ) external reentrancyGuardInitializer returns (bytes32) {
```

### Using unchecked blocks to save gas - Increments in for loop can be unchecked  ( save 30-40 gas per loop iteration)
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let's work with a sample loop below.

```solidity
for(uint256 i; i < 10; i++){
//doSomething
}

```
can be written as shown below.
```solidity
for(uint256 i; i < 10;) {
  // loop logic
  unchecked { i++; }
}
```

We can also write  it as an inlined function like below.

```solidity
function inc(i) internal pure returns (uint256) {
  unchecked { return i + 1; }
}
for(uint256 i; i < 10; i = inc(i)) {
  // doSomething
}
```

**Affected code**

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L94
```solidity
File: /ethereum/contracts/zksync/libraries/Diamond.sol
94:        for (uint256 i = 0; i < facetCutsLength; ++i) {
```

**Other Instances to modify**
https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L132
```solidity
File: /ethereum/contracts/zksync/libraries/Diamond.sol
132:        for (uint256 i = 0; i < selectorsLength; ++i) {
```

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L153
```solidity
File: /ethereum/contracts/zksync/libraries/Diamond.sol
153:        for (uint256 i = 0; i < selectorsLength; ++i) {
```

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L173
```solidity
File: /ethereum/contracts/zksync/libraries/Diamond.sol
173:        for (uint256 i = 0; i < selectorsLength; ++i) {
```

[see resource](https://github.com/ethereum/solidity/issues/10695)

### Splitting require() statements that use && saves gas - (saves 8 gas per &&)

Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 8 GAS per &&
The gas difference would only be realized if the revert condition is realized(met).

**Proof**
**The following tests were carried out in remix with both optimization turned on and off**

```solidity
function multiple (uint a) public pure returns (uint){
	require ( a > 1 && a < 5, "Initialized");
	return  a + 2;
}
```
**Execution cost**
21617 with optimization and using &&
21976 without optimization and using &&


After splitting the require statement
```solidity
function multiple(uint a) public pure returns (uint){
	require (a > 1 ,"Initialized");
	require (a < 5 , "Initialized");
	return a + 2;
}
```
**Execution cost**
21609 with optimization and split require
21968 without optimization and using split require

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/AllowList.sol#L96-L101
```solidity
File: /ethereum/contracts/common/AllowList.sol
96:        require(
97:            callersLength == _targets.length &&
98:                callersLength == _functionSigs.length &&
99:                callersLength == _enables.length,
100:            "yw"
101:        ); // The size of arrays should be equal
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/L2ContractHelper.sol#L65
```solidity
File: /ethereum/contracts/common/L2ContractHelper.sol
65:        require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
```

### Use a more recent version of solidity

Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/DiamondProxy.sol#L3
```solidity
File: /ethereum/contracts/zksync/DiamondProxy.sol
3:pragma solidity ^0.8.0;
```

All the contracts in scope seems to be using the above version


