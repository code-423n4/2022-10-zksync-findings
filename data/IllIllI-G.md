
## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Remove or replace unused state variables | 2 | - |
| [G&#x2011;02] | Structs can be packed into fewer storage slots | 2 | - |
| [G&#x2011;03] | Using `storage` instead of `memory` for structs/arrays saves gas | 5 | 21000 |
| [G&#x2011;04] | State variables should be cached in stack variables rather than re-reading them from storage | 2 | 194 |
| [G&#x2011;05] | `internal` functions only called once can be inlined to save gas | 24 | 480 |
| [G&#x2011;06] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 4 | 240 |
| [G&#x2011;07] | Optimize names to save gas | 28 | 616 |
| [G&#x2011;08] | `internal` functions not called by the contract should be removed to save deployment gas | 1 | - |
| [G&#x2011;09] | Splitting `require()` statements that use `&&` saves gas | 2 | 6 |
| [G&#x2011;10] | `require()` or `revert()` statements that check input arguments should be at the top of the function | 1 | - |
| [G&#x2011;11] | Superfluous event fields | 2 | - |
| [G&#x2011;12] | Functions guaranteed to revert when called by normal users can be marked `payable` | 23 | 483 |

Total: 96 instances over 12 issues with **23019 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.



## Gas Optimizations

### [G&#x2011;01]  Remove or replace unused state variables
Saves a storage slot. If the variable is assigned a non-zero value, saves Gsset (**20000 gas**). If it's assigned a zero value, saves Gsreset (**2900 gas**). If the variable remains unassigned, there is no gas savings unless the variable is `public`, in which case the compiler-generated non-payable getter deployment cost is saved. If the state variable is overriding an interface's public function, mark the variable as `constant` or `immutable` so that it does not use a storage slot

*There are 2 instances of this issue:*
```solidity
File: zksync/contracts/bridge/L2ETHBridge.sol

16:       uint256 public totalSupply;

19:       mapping(address => uint256) public balanceOf;

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ETHBridge.sol#L16

### [G&#x2011;02]  Structs can be packed into fewer storage slots
Each slot saved can avoid an extra Gsset (**20000 gas**) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

*There are 2 instances of this issue:*
```solidity
File: ethereum/contracts/zksync/interfaces/IExecutor.sol

/// @audit Variable ordering with 7 slots instead of the current 8:
///           make `indexRepeatedStorageChanges` come right after `blockNumber`
15        struct StoredBlockInfo {
16            uint64 blockNumber;
17            bytes32 blockHash;
18            uint64 indexRepeatedStorageChanges;
19            uint256 numberOfLayer1Txs;
20            bytes32 priorityOperationsHash;
21            bytes32 l2LogsTreeRoot;
22            uint256 timestamp;
23            bytes32 commitment;
24:       }

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IExecutor.sol#L15-L24

```solidity
File: ethereum/contracts/zksync/Storage.sol

/// @audit Variable ordering with 13 slots instead of the current 14:
///           make `zkPorterIsAvailable` come right after `governor`
69    struct AppStorage {
70        /// @dev Storage of variables needed for diamond cut facet
71        DiamondCutStorage diamondCutStorage;
72        /// @notice Address which will exercise governance over the network i.e. change validator set, conduct upgrades
73        address governor;
74        /// @notice Address that governor proposed as one that will replace it
75        address pendingGovernor;
76        /// @notice List of permitted validators
77        mapping(address => bool) validators;
78        /// @dev Verifier contract. Used to verify aggregated proof for blocks
79        Verifier verifier;
80        /// @notice Total number of executed blocks i.e. blocks[totalBlocksExecuted] points at the latest executed block (block 0 is genesis)
81        uint256 totalBlocksExecuted;
82        /// @notice Total number of proved blocks i.e. blocks[totalBlocksProved] points at the latest proved block
83        uint256 totalBlocksVerified;
84        /// @notice Total number of committed blocks i.e. blocks[totalBlocksCommitted] points at the latest committed block
85        uint256 totalBlocksCommitted;
86        /// @dev Stored hashed StoredBlock for block number
87        mapping(uint256 => bytes32) storedBlockHashes;
88        /// @dev Stored root hashes of L2 -> L1 logs
89        mapping(uint256 => bytes32) l2LogsRootHashes;
90        /// @dev Container that stores transactions requested from L1
91        PriorityQueue.Queue priorityQueue;
92        /// @dev The smart contract that manages the list with permission to call contract functions
93        IAllowList allowList;
94        /// @notice Part of the configuration parameters of ZKP circuits. Used as an input for the verifier smart contract
95        VerifierParams verifierParams;
96        /// @notice Bytecode hash of bootloader program.
97        /// @dev Used as an input to zkp-circuit.
98        bytes32 l2BootloaderBytecodeHash;
99        /// @notice Bytecode hash of default account (bytecode for EOA).
100       /// @dev Used as an input to zkp-circuit.
101       bytes32 l2DefaultAccountBytecodeHash;
102       /// @dev Indicates that the porter may be touched on L2 transactions.
103       /// @dev Used as an input to zkp-circuit.
104       bool zkPorterIsAvailable;
105:  }

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/Storage.sol#L69-L105

### [G&#x2011;03]  Using `storage` instead of `memory` for structs/arrays saves gas
When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (**2100 gas**) for *each* field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct

*There are 5 instances of this issue:*
```solidity
File: ethereum/contracts/zksync/DiamondProxy.sol

26:           Diamond.SelectorToFacet memory facet = diamondStorage.selectorToFacet[msg.sig];

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/DiamondProxy.sol#L26

```solidity
File: ethereum/contracts/zksync/facets/Getters.sol

165:              Diamond.FacetToSelectors memory facetToSelectors = ds.facetToSelectors[facetAddr];

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Getters.sol#L165

```solidity
File: ethereum/contracts/zksync/libraries/Diamond.sol

134:              SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];

155:              SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];

175:              SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L134


Code changes:
```diff
diff --git a/ethereum/contracts/zksync/DiamondProxy.sol b/ethereum/contracts/zksync/DiamondProxy.sol
index 6240c9a..00fa316 100644
--- a/ethereum/contracts/zksync/DiamondProxy.sol
+++ b/ethereum/contracts/zksync/DiamondProxy.sol
@@ -23,7 +23,7 @@ contract DiamondProxy {
         // which is not defined for data length in range [1, 3].
         require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
         // Get facet from function selector
-        Diamond.SelectorToFacet memory facet = diamondStorage.selectorToFacet[msg.sig];
+        Diamond.SelectorToFacet storage facet = diamondStorage.selectorToFacet[msg.sig];
         address facetAddress = facet.facetAddress;
 
         require(facetAddress != address(0), "F"); // Proxy has no facet for this selector
diff --git a/ethereum/contracts/zksync/facets/Getters.sol b/ethereum/contracts/zksync/facets/Getters.sol
index f86990a..d5a51b1 100644
--- a/ethereum/contracts/zksync/facets/Getters.sol
+++ b/ethereum/contracts/zksync/facets/Getters.sol
@@ -162,7 +162,7 @@ contract GettersFacet is Base, IGetters {
 
         for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
             address facetAddr = ds.facets[i];
-            Diamond.FacetToSelectors memory facetToSelectors = ds.facetToSelectors[facetAddr];
+            Diamond.FacetToSelectors storage facetToSelectors = ds.facetToSelectors[facetAddr];
 
             result[i] = Facet(facetAddr, facetToSelectors.selectors);
         }
diff --git a/ethereum/contracts/zksync/libraries/Diamond.sol b/ethereum/contracts/zksync/libraries/Diamond.sol
index d2d8e83..7e35248 100644
--- a/ethereum/contracts/zksync/libraries/Diamond.sol
+++ b/ethereum/contracts/zksync/libraries/Diamond.sol
@@ -131,7 +131,7 @@ library Diamond {
         uint256 selectorsLength = _selectors.length;
         for (uint256 i = 0; i < selectorsLength; ++i) {
             bytes4 selector = _selectors[i];
-            SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
+            SelectorToFacet storage oldFacet = ds.selectorToFacet[selector];
             require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists
 
             _addOneFunction(_facet, selector, _isFacetFreezable);
@@ -152,7 +152,7 @@ library Diamond {
         uint256 selectorsLength = _selectors.length;
         for (uint256 i = 0; i < selectorsLength; ++i) {
             bytes4 selector = _selectors[i];
-            SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
+            SelectorToFacet storage oldFacet = ds.selectorToFacet[selector];
             require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address
 
             _removeOneFunction(oldFacet.facetAddress, selector);
@@ -172,7 +172,7 @@ library Diamond {
         uint256 selectorsLength = _selectors.length;
         for (uint256 i = 0; i < selectorsLength; ++i) {
             bytes4 selector = _selectors[i];
-            SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
+            SelectorToFacet storage oldFacet = ds.selectorToFacet[selector];
             require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet
 
             _removeOneFunction(oldFacet.facetAddress, selector);

```

Gas changes:
```diff
-|  DiamondCutFacet                                    ·          -  ·          -  ·    1686842  ·        5.6 %  ·          -  │
+|  DiamondCutFacet                                    ·          -  ·          -  ·    1658939  ·        5.5 %  ·          -  │
-|  DiamondCutTest                                     ·          -  ·          -  ·    1664429  ·        5.5 %  ·          -  │
+|  DiamondCutTest                                     ·          -  ·          -  ·    1626312  ·        5.4 %  ·          -  │
-|  DiamondInit                                        ·          -  ·          -  ·     293178  ·          1 %  ·          -  │
+|  DiamondInit                                        ·          -  ·          -  ·     293166  ·          1 %  ·          -  │
-|  DiamondProxy                                       ·    1406590  ·    2537898  ·    2087482  ·          7 %  ·          -  │
+|  DiamondProxy                                       ·    1366496  ·    2493112  ·    2044502  ·        6.8 %  ·          -  │
-|  GettersFacet                                       ·          -  ·          -  ·     716653  ·        2.4 %  ·          -  │
+|  GettersFacet                                       ·          -  ·          -  ·     706506  ·        2.4 %  ·          -  │
```


### [G&#x2011;04]  State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 2 instances of this issue:*
```solidity
File: ethereum/contracts/bridge/L1ERC20Bridge.sol

/// @audit l2ProxyTokenBytecodeHash on line 78
82:           bytes memory create2Input = abi.encode(address(this), l2ProxyTokenBytecodeHash, _governor);

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L82

```solidity
File: zksync/contracts/bridge/L2StandardERC20.sol

/// @audit decimals_ on line 86
92:           emit BridgeInitialization(_l1Address, decodedName, decodedSymbol, decimals_);

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2StandardERC20.sol#L92

### [G&#x2011;05]  `internal` functions only called once can be inlined to save gas
Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

*There are 24 instances of this issue:*
```solidity
File: ethereum/contracts/bridge/L1ERC20Bridge.sol

136       function _depositFunds(
137           address _from,
138           IERC20 _token,
139           uint256 _amount
140:      ) internal returns (uint256) {

149       function _getDepositL2Calldata(
150           address _l1Sender,
151           address _l2Receiver,
152           address _l1Token,
153           uint256 _amount
154:      ) internal view returns (bytes memory txCalldata) {

164:      function _getERC20Getters(address _token) internal view returns (bytes memory data) {

260       function _parseL2WithdrawalMessage(bytes memory _l2ToL1message)
261           internal
262           pure
263           returns (
264               address l1Receiver,
265               address l1Token,
266:              uint256 amount

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L136-L140

```solidity
File: ethereum/contracts/bridge/L1EthBridge.sol

113       function _getDepositL2Calldata(
114           address _l1Sender,
115           address _l2Receiver,
116           uint256 _amount
117:      ) internal pure returns (bytes memory txCalldata) {

214       function _parseL2WithdrawalMessage(bytes memory _message)
215           internal
216           pure
217:          returns (address l1Receiver, uint256 amount)

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L113-L117

```solidity
File: ethereum/contracts/zksync/facets/Executor.sol

23        function _commitOneBlock(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)
24            internal
25            view
26:           returns (StoredBlockInfo memory storedNewBlock)

89        function _processL2Logs(CommitBlockInfo calldata _newBlock)
90            internal
91            pure
92            returns (
93                uint256 numberOfLayer1Txs,
94                bytes32 chainedPriorityTxsHash,
95                bytes32 previousBlockHash,
96:               uint256 blockTimestamp

177:      function _collectOperationsFromPriorityQueue(uint256 _nPriorityOps) internal returns (bytes32 concatHash) {

190:      function _executeOneBlock(StoredBlockInfo memory _storedBlock, uint256 _executedBlockIdx) internal {

274       function _getBlockProofPublicInput(
275           bytes32 _prevBlockCommitment,
276           bytes32 _currentBlockCommitment,
277           ProofInput calldata _proof,
278           VerifierParams memory _verifierParams
279:      ) internal pure returns (uint256) {

296:      function _verifyRecursivePartOfProof(uint256[] calldata _recurisiveAggregationInput) internal view returns (bool) {

349:      function _maxU256(uint256 a, uint256 b) internal pure returns (uint256) {

354:      function _createBlockCommitment(CommitBlockInfo calldata _newBlockData) internal view returns (bytes32) {

362:      function _blockPassThroughData(CommitBlockInfo calldata _block) internal pure returns (bytes memory) {

372:      function _blockMetaParameters() internal view returns (bytes memory) {

376:      function _blockAuxilaryOutput(CommitBlockInfo calldata _block) internal pure returns (bytes memory) {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L23-L26

```solidity
File: ethereum/contracts/zksync/facets/Mailbox.sol

75:       function _L2MessageToLog(L2Message calldata _message) internal pure returns (L2Log memory) {

116       function _requestL2Transaction(
117           address _sender,
118           address _contractL2,
119           uint256 _l2Value,
120           bytes calldata _calldata,
121           uint256 _ergsLimit,
122           bytes[] calldata _factoryDeps
123:      ) internal returns (bytes32 canonicalTxHash) {

144       function _writePriorityOp(
145           address _sender,
146           uint256 _txId,
147           uint256 _l2Value,
148           address _contractAddressL2,
149           bytes calldata _calldata,
150           uint64 _expirationBlock,
151           uint256 _ergsLimit,
152           bytes[] calldata _factoryDeps
153:      ) internal returns (bytes32 canonicalTxHash) {

216       function _hashFactoryDeps(bytes[] calldata _factoryDeps)
217           internal
218           pure
219:          returns (uint256[] memory hashedFactoryDeps)

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Mailbox.sol#L75

```solidity
File: zksync/contracts/bridge/L2ERC20Bridge.sol

73:       function _deployL2Token(address _l1Token, bytes calldata _data) internal returns (address) {

104       function _getL1WithdrawMessage(
105           address _to,
106           address _l1Token,
107           uint256 _amount
108:      ) internal pure returns (bytes memory) {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ERC20Bridge.sol#L73

```solidity
File: zksync/contracts/bridge/L2ETHBridge.sol

74:       function _getL1WithdrawMessage(address _to, uint256 _amount) internal pure returns (bytes memory) {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ETHBridge.sol#L74

### [G&#x2011;06]  `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**

*There are 4 instances of this issue:*
```solidity
File: ethereum/contracts/zksync/libraries/Diamond.sol

94:           for (uint256 i = 0; i < facetCutsLength; ++i) {

132:          for (uint256 i = 0; i < selectorsLength; ++i) {

153:          for (uint256 i = 0; i < selectorsLength; ++i) {

173:          for (uint256 i = 0; i < selectorsLength; ++i) {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L94

### [G&#x2011;07]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

*There are 28 instances of this issue:*
```solidity
File: ethereum/contracts/bridge/interfaces/IL1Bridge.sol

/// @audit isWithdrawalFinalized(), deposit(), claimFailedDeposit(), finalizeWithdrawal(), l2TokenAddress()
8:    interface IL1Bridge {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L8

```solidity
File: ethereum/contracts/bridge/interfaces/IL2Bridge.sol

/// @audit finalizeDeposit(), withdraw(), l1TokenAddress(), l2TokenAddress(), l1Bridge()
6:    interface IL2Bridge {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/interfaces/IL2Bridge.sol#L6

```solidity
File: ethereum/contracts/bridge/L1ERC20Bridge.sol

/// @audit initialize(), deposit(), claimFailedDeposit(), finalizeWithdrawal(), l2TokenAddress()
21:   contract L1ERC20Bridge is IL1Bridge, AllowListed, ReentrancyGuard {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L21

```solidity
File: ethereum/contracts/bridge/L1EthBridge.sol

/// @audit initialize(), deposit(), l2TokenAddress()
17:   contract L1EthBridge is IL1Bridge, AllowListed, ReentrancyGuard {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L17

```solidity
File: ethereum/contracts/common/AllowList.sol

/// @audit canCall(), setPublicAccess(), setBatchPublicAccess(), setBatchPermissionToCall(), setPermissionToCall(), setPendingOwner(), acceptOwner()
16:   contract AllowList is IAllowList {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/AllowList.sol#L16

```solidity
File: ethereum/contracts/common/interfaces/IAllowList.sol

/// @audit pendingOwner(), isAccessPublic(), hasSpecialAccessToCall(), canCall(), setBatchPublicAccess(), setPublicAccess(), setBatchPermissionToCall(), setPermissionToCall(), setPendingOwner(), acceptOwner()
5:    interface IAllowList {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/interfaces/IAllowList.sol#L5

```solidity
File: ethereum/contracts/common/L2ContractHelper.sol

/// @audit sendToL1()
5:    interface IL2Messenger {

/// @audit forceDeployOnAddresses(), create2()
9:    interface IContractDeployer {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/L2ContractHelper.sol#L5

```solidity
File: ethereum/contracts/zksync/DiamondInit.sol

/// @audit initialize()
14:   contract DiamondInit is Base {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/DiamondInit.sol#L14

```solidity
File: ethereum/contracts/zksync/facets/DiamondCut.sol

/// @audit proposeDiamondCut(), cancelDiamondCutProposal(), executeDiamondCutProposal(), emergencyFreezeDiamond(), unfreezeDiamond(), approveEmergencyDiamondCutAsSecurityCouncilMember()
12:   contract DiamondCutFacet is Base, IDiamondCut {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/DiamondCut.sol#L12

```solidity
File: ethereum/contracts/zksync/facets/Executor.sol

/// @audit executeBlocks(), proveBlocks(), revertBlocks()
16:   contract ExecutorFacet is Base, IExecutor {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L16

```solidity
File: ethereum/contracts/zksync/facets/Getters.sol

/// @audit getVerifier(), getGovernor(), getPendingGovernor(), getTotalBlocksCommitted(), getTotalBlocksVerified(), getTotalBlocksExecuted(), getTotalPriorityTxs(), getFirstUnprocessedPriorityTx(), getPriorityQueueSize(), priorityQueueFrontOperation(), isValidator(), l2LogsRootHash(), storedBlockHash(), getProposedDiamondCutHash(), getProposedDiamondCutTimestamp(), getLastDiamondFreezeTimestamp(), getCurrentProposalId(), getSecurityCouncilEmergencyApprovals(), isSecurityCouncilMember(), getSecurityCouncilMemberLastApprovedProposalId(), isDiamondStorageFrozen(), isFacetFreezable(), isFunctionFreezable(), facets(), facetFunctionSelectors(), facetAddresses(), facetAddress()
13:   contract GettersFacet is Base, IGetters {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Getters.sol#L13

```solidity
File: ethereum/contracts/zksync/facets/Governance.sol

/// @audit setPendingGovernor(), acceptGovernor(), setValidator(), setL2BootloaderBytecodeHash(), setL2DefaultAccountBytecodeHash(), setPorterAvailability(), setVerifier(), setVerifierParams()
11:   contract GovernanceFacet is Base, IGovernance {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Governance.sol#L11

```solidity
File: ethereum/contracts/zksync/facets/Mailbox.sol

/// @audit proveL2MessageInclusion(), proveL2LogInclusion(), l2TransactionBaseCost(), requestL2Transaction(), serializeL2Transaction()
16:   contract MailboxFacet is Base, IMailbox {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Mailbox.sol#L16

```solidity
File: ethereum/contracts/zksync/interfaces/IDiamondCut.sol

/// @audit proposeDiamondCut(), cancelDiamondCutProposal(), executeDiamondCutProposal(), emergencyFreezeDiamond(), unfreezeDiamond(), approveEmergencyDiamondCutAsSecurityCouncilMember()
7:    interface IDiamondCut {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L7

```solidity
File: ethereum/contracts/zksync/interfaces/IExecutor.sol

/// @audit commitBlocks(), proveBlocks(), executeBlocks(), revertBlocks()
5:    interface IExecutor {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IExecutor.sol#L5

```solidity
File: ethereum/contracts/zksync/interfaces/IGetters.sol

/// @audit getVerifier(), getGovernor(), getPendingGovernor(), getTotalBlocksCommitted(), getTotalBlocksVerified(), getTotalBlocksExecuted(), getTotalPriorityTxs(), getFirstUnprocessedPriorityTx(), getPriorityQueueSize(), priorityQueueFrontOperation(), isValidator(), l2LogsRootHash(), storedBlockHash(), isDiamondStorageFrozen(), getProposedDiamondCutHash(), getProposedDiamondCutTimestamp(), getLastDiamondFreezeTimestamp(), getCurrentProposalId(), getSecurityCouncilEmergencyApprovals(), isSecurityCouncilMember(), getSecurityCouncilMemberLastApprovedProposalId(), facets(), facetFunctionSelectors(), facetAddresses(), facetAddress(), isFunctionFreezable()
7:    interface IGetters {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IGetters.sol#L7

```solidity
File: ethereum/contracts/zksync/interfaces/IGovernance.sol

/// @audit setPendingGovernor(), acceptGovernor(), setValidator(), setL2BootloaderBytecodeHash(), setL2DefaultAccountBytecodeHash(), setPorterAvailability(), setVerifier(), setVerifierParams()
8:    interface IGovernance {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IGovernance.sol#L8

```solidity
File: ethereum/contracts/zksync/interfaces/IMailbox.sol

/// @audit proveL2MessageInclusion(), proveL2LogInclusion(), serializeL2Transaction(), requestL2Transaction(), l2TransactionBaseCost()
7:    interface IMailbox {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IMailbox.sol#L7

```solidity
File: zksync/contracts/bridge/interfaces/IL1Bridge.sol

/// @audit finalizeWithdrawal()
6:    interface IL1Bridge {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/interfaces/IL1Bridge.sol#L6

```solidity
File: zksync/contracts/bridge/interfaces/IL2Bridge.sol

/// @audit finalizeDeposit(), withdraw(), l1TokenAddress(), l2TokenAddress(), l1Bridge()
6:    interface IL2Bridge {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/interfaces/IL2Bridge.sol#L6

```solidity
File: zksync/contracts/bridge/interfaces/IL2EthInitializable.sol

/// @audit initialization()
5:    interface IL2EthInitializable {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/interfaces/IL2EthInitializable.sol#L5

```solidity
File: zksync/contracts/bridge/interfaces/IL2StandardToken.sol

/// @audit bridgeMint(), bridgeBurn(), l1Address(), l2Bridge()
5:    interface IL2StandardToken {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/interfaces/IL2StandardToken.sol#L5

```solidity
File: zksync/contracts/bridge/L2ETHBridge.sol

/// @audit finalizeDeposit(), l2TokenAddress()
14:   contract L2ETHBridge is IL2Bridge {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ETHBridge.sol#L14

```solidity
File: zksync/contracts/bridge/L2StandardERC20.sol

/// @audit bridgeInitialize()
11:   contract L2StandardERC20 is ERC20Upgradeable, IL2StandardToken {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2StandardERC20.sol#L11

```solidity
File: zksync/contracts/ExternalDecoder.sol

/// @audit decodeString(), decodeUint8()
8:    library ExternalDecoder {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/ExternalDecoder.sol#L8

```solidity
File: zksync/contracts/L2ContractHelper.sol

/// @audit sendToL1()
5:    interface IL2Messenger {

/// @audit create2()
9:    interface IContractDeployer {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/L2ContractHelper.sol#L5

### [G&#x2011;08]  `internal` functions not called by the contract should be removed to save deployment gas
If the functions are required by an interface, the contract should inherit from that interface and use the `override` keyword

*There is 1 instance of this issue:*
```solidity
File: ethereum/contracts/zksync/facets/Executor.sol

80        function _calculateBlockHash(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)
81            internal
82            pure
83:           returns (bytes32)

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L80-L83

### [G&#x2011;09]  Splitting `require()` statements that use `&&` saves gas
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas**

*There are 2 instances of this issue:*
```solidity
File: ethereum/contracts/common/AllowList.sol

96            require(
97                callersLength == _targets.length &&
98                    callersLength == _functionSigs.length &&
99                    callersLength == _enables.length,
100               "yw"
101:          ); // The size of arrays should be equal

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/AllowList.sol#L96-L101

```solidity
File: ethereum/contracts/common/L2ContractHelper.sol

65:           require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/L2ContractHelper.sol#L65

### [G&#x2011;10]  `require()` or `revert()` statements that check input arguments should be at the top of the function
Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (**2100 gas***) in a function that may ultimately revert in the unhappy case.

*There is 1 instance of this issue:*
```solidity
File: zksync/contracts/bridge/L2ETHBridge.sol

/// @audit expensive op on line 49
50:           require(_l1Token == CONVENTIONAL_ETH_ADDRESS);

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ETHBridge.sol#L50

### [G&#x2011;11]  Superfluous event fields
`block.timestamp` and `block.number` are added to event information by default so adding them manually wastes gas

*There are 2 instances of this issue:*
```solidity
File: ethereum/contracts/zksync/interfaces/IExecutor.sol

76:       event BlockCommit(uint256 indexed blockNumber, bytes32 indexed blockHash, bytes32 indexed commitment);

82:       event BlockExecution(uint256 indexed blockNumber, bytes32 indexed blockHash, bytes32 indexed commitment);

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IExecutor.sol#L76

### [G&#x2011;12]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 23 instances of this issue:*
```solidity
File: ethereum/contracts/common/AllowList.sol

57:       function setPublicAccess(address _target, bool _enable) external onlyOwner {

65:       function setBatchPublicAccess(address[] calldata _targets, bool[] calldata _enables) external onlyOwner {

89        function setBatchPermissionToCall(
90            address[] calldata _callers,
91            address[] calldata _targets,
92            bytes4[] calldata _functionSigs,
93            bool[] calldata _enables
94:       ) external onlyOwner {

113       function setPermissionToCall(
114           address _caller,
115           address _target,
116           bytes4 _functionSig,
117           bool _enable
118:      ) external onlyOwner {

140:      function setPendingOwner(address _newPendingOwner) external onlyOwner {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/AllowList.sol#L57

```solidity
File: ethereum/contracts/zksync/facets/DiamondCut.sol

22:       function proposeDiamondCut(Diamond.FacetCut[] calldata _facetCuts, address _initAddress) external onlyGovernor {

35:       function cancelDiamondCutProposal() external onlyGovernor {

46:       function executeDiamondCutProposal(Diamond.DiamondCutData calldata _diamondCut) external onlyGovernor {

78:       function emergencyFreezeDiamond() external onlyGovernor {

91:       function unfreezeDiamond() external onlyGovernor {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/DiamondCut.sol#L22

```solidity
File: ethereum/contracts/zksync/facets/Executor.sol

152       function commitBlocks(StoredBlockInfo memory _lastCommittedBlockData, CommitBlockInfo[] calldata _newBlocksData)
153           external
154           override
155           nonReentrant
156:          onlyValidator

208:      function executeBlocks(StoredBlockInfo[] calldata _blocksData) external nonReentrant onlyValidator {

221       function proveBlocks(
222           StoredBlockInfo calldata _prevBlock,
223           StoredBlockInfo[] calldata _committedBlocks,
224           ProofInput calldata _proof
225:      ) external nonReentrant onlyValidator {

336:      function revertBlocks(uint256 _newLastBlock) external nonReentrant onlyValidator {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L152-L156

```solidity
File: ethereum/contracts/zksync/facets/Governance.sol

15:       function setPendingGovernor(address _newPendingGovernor) external onlyGovernor {

45:       function setValidator(address _validator, bool _active) external onlyGovernor {

54:       function setL2BootloaderBytecodeHash(bytes32 _l2BootloaderBytecodeHash) external onlyGovernor {

69:       function setL2DefaultAccountBytecodeHash(bytes32 _l2DefaultAccountBytecodeHash) external onlyGovernor {

84:       function setPorterAvailability(bool _zkPorterIsAvailable) external onlyGovernor {

94:       function setVerifier(Verifier _newVerifier) external onlyGovernor {

104:      function setVerifierParams(VerifierParams calldata _newVerifierParams) external onlyGovernor {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Governance.sol#L15

```solidity
File: zksync/contracts/bridge/L2StandardERC20.sol

102:      function bridgeMint(address _to, uint256 _amount) external override onlyBridge {

109:      function bridgeBurn(address _from, uint256 _amount) external override onlyBridge {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2StandardERC20.sol#L102

___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | `<array>.length` should not be looked up in every loop of a `for`-loop | 1 | 3 |
| [G&#x2011;02] | Using `bool`s for storage incurs overhead | 4 | 68400 |

Total: 5 instances over 2 issues with **68403 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.



## Gas Optimizations

### [G&#x2011;01]  `<array>.length` should not be looked up in every loop of a `for`-loop
The overheads outlined below are _PER LOOP_, excluding the first loop
* storage arrays incur a Gwarmaccess (**100 gas**)
* memory arrays use `MLOAD` (**3 gas**)
* calldata arrays use `CALLDATALOAD` (**3 gas**)

Caching the length changes each of these to a `DUP<N>` (**3 gas**), and gets rid of the extra `DUP<N>` needed to store the stack offset

*There is 1 instance of this issue:*
```solidity
File: ethereum/contracts/zksync/facets/Executor.sol

/// @audit (valid but excluded finding)
111:          for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L111

### [G&#x2011;02]  Using `bool`s for storage incurs overhead
```solidity
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess (**[100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)**) for the extra SLOAD, and to avoid Gsset (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past

*There are 4 instances of this issue:*
```solidity
File: ethereum/contracts/bridge/L1ERC20Bridge.sol

/// @audit (valid but excluded finding)
40:       mapping(uint256 => mapping(uint256 => bool)) public isWithdrawalFinalized;

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L40

```solidity
File: ethereum/contracts/bridge/L1EthBridge.sol

/// @audit (valid but excluded finding)
37:       mapping(uint256 => mapping(uint256 => bool)) public isWithdrawalFinalized;

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L37

```solidity
File: ethereum/contracts/common/AllowList.sol

/// @audit (valid but excluded finding)
26:       mapping(address => bool) public isAccessPublic;

/// @audit (valid but excluded finding)
30:       mapping(address => mapping(address => mapping(bytes4 => bool))) public hasSpecialAccessToCall;

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/AllowList.sol#L26
