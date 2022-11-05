## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Contexts|
|-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops | 4 |
| [GAS&#x2011;2](#GAS&#x2011;2) | Splitting `require()` Statements That Use `&&` Saves Gas | 2 |
| [GAS&#x2011;3](#GAS&#x2011;3) | `abi.encode()` is less efficient than `abi.encodepacked()` | 17 |
| [GAS&#x2011;4](#GAS&#x2011;4) | Use calldata instead of memory for function parameters | 3 |
| [GAS&#x2011;5](#GAS&#x2011;5) | Public Functions To External | 2 |
| [GAS&#x2011;6](#GAS&#x2011;6) | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 11 |
| [GAS&#x2011;7](#GAS&#x2011;7) | Optimize names to save gas | All in-scope contracts | 
| [GAS&#x2011;8](#GAS&#x2011;8) | Use `uint256(1)`/`uint256(2)` instead for `true` and `false` boolean states | 16 |
| [GAS&#x2011;9](#GAS&#x2011;9) | Structs can be packed into fewer storage slots | 2 |
| [GAS&#x2011;10](#GAS&#x2011;10) | Superfluous event fields | 2 |
| [GAS&#x2011;11](#GAS&#x2011;11) | Do not calculate constants | 3 |



Total: 80 contexts over 11 issues

## Gas Optimizations


### <a href="#Summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

#### <ins>Proof Of Concept</ins>


```
94: for (uint256 i = 0; i < facetCutsLength; ++i) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L94

```
132: for (uint256 i = 0; i < selectorsLength; ++i) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L132

```
153: for (uint256 i = 0; i < selectorsLength; ++i) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L153

```
173: for (uint256 i = 0; i < selectorsLength; ++i) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L173





### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> Splitting `require()` statements that use `&&` saves gas

Instead of using operator `&&` on a single `require`. Using a two `require` can save more gas.

i.e.
for `require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf");` use:

```
	require(version == 1);
	require(_bytecodeHash[1] == bytes1(0));
```

#### <ins>Proof Of Concept</ins>

```
65: require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol#L65

```
65: require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol#L65





### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> `abi.encode()` is less efficient than `abi.encodepacked()`

See for more information: https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison 

#### <ins>Proof Of Concept</ins>


```
82: bytes memory create2Input = abi.encode(address(this), l2ProxyTokenBytecodeHash, _governor);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L82

```
168: data = abi.encode(data1, data2, data3);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L168

```
283: bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L283

```
58: bytes memory create2Input = abi.encode(address(this));
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L58

```
54: s.storedBlockHashes[0] = keccak256(abi.encode(storedBlockZero));
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondInit.sol#L54

```
27: s.diamondCutStorage.proposedDiamondCutHash = keccak256(abi.encode(_facetCuts, _initAddress));
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L27

```
61: keccak256(abi.encode(_diamondCut.facetCuts, _diamondCut.initAddress)),
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L61

```
85: return keccak256(abi.encode(_previousBlock.blockHash, _newBlock.newStateRoot));
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L85

```
122: chainedPriorityTxsHash = keccak256(abi.encode(chainedPriorityTxsHash, canonicalTxHash));
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L122

```
182: concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L182

```
359: return keccak256(abi.encode(passThroughDataHash, metadataHash, auxiliaryOutputHash));
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L359

```
381: return abi.encode(_block.l2LogsTreeRoot, l2ToL1LogsHash, initialStorageChangesHash, repeatedStorageChangesHash);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L381

```
386: return keccak256(abi.encode(_storedBlockInfo));
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L386

```
163: canonicalTxHash = keccak256(abi.encode(transaction));
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol#L163

```
30: currentHash = keccak256(abi.encode(currentHash, _path[i]));
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Merkle.sol#L30

```
32: currentHash = keccak256(abi.encode(_path[i], currentHash));
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Merkle.sol#L32

```
114: bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L114



### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> Use calldata instead of memory for function parameters

In some cases, having function arguments in calldata instead of
memory is more optimal.

Consider the following generic example:
```
contract C {
	function add(uint[] memory arr) external returns (uint sum) {
		uint length = arr.length;
		for (uint i = 0; i < arr.length; i++) {
		    sum += arr[i];
		}
	}
}
```
In the above example, the dynamic array arr has the storage location
memory. When the function gets called externally, the array values are
kept in calldata and copied to memory during ABI decoding (using the
opcode calldataload and mstore). And during the for loop, arr[i]
accesses the value in memory using a mload. However, for the above
example this is inefficient. Consider the following snippet instead:
```
contract C {
	function add(uint[] calldata arr) external returns (uint sum) {
		uint length = arr.length;
		for (uint i = 0; i < arr.length; i++) {
		    sum += arr[i];
		}
	}
}
```
In the above snippet, instead of going via memory, the value is directly
read from calldata using calldataload. That is, there are no
intermediate memory operations that carries this value.

Gas savings: In the former example, the ABI decoding begins with
copying value from calldata to memory in a for loop. Each iteration
would cost at least 60 gas. In the latter example, this can be
completely avoided. This will also reduce the number of instructions and
therefore reduces the deploy time cost of the contract.

In short, use calldata instead of memory if the function argument
is only read.

Note that in older Solidity versions, changing some function arguments
from memory to calldata may cause "unimplemented feature error".
This can be avoided by using a newer (0.8.*) Solidity compiler.

Examples
Note: The following pattern is prevalent in the codebase:
```
function f(bytes memory data) external {
	(...) = abi.decode(data, (..., types, ...));
}
```
Here, changing to bytes calldata will decrease the gas. The total
savings for this change across all such uses would be quite
significant.

#### <ins>Proof Of Concept</ins>


```
function _addFunctions(
        address _facet,
        bytes4[] memory _selectors,
        bool _isFacetFreezable
    ) private {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L119

```
function _replaceFunctions(
        address _facet,
        bytes4[] memory _selectors,
        bool _isFacetFreezable
    ) private {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L143

```
function _removeFunctions(address _facet, bytes4[] memory _selectors) private {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L167






### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> Public Functions To External

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

#### <ins>Proof Of Concept</ins>

```
function l2TransactionBaseCost(
        uint256, 
        uint256, 
        uint32 
    ) public pure returns (uint256) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol#L89

```
function l2TokenAddress(address _l1Token) public view override returns (address) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L113




### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

#### <ins>Proof Of Concept</ins>

```
26: uint160 constant SYSTEM_CONTRACTS_OFFSET = 0x8000;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol#L26

```
64: uint8 version = uint8(_bytecodeHash[0]);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol#L64

```
40: uint8 l2ShardId;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Storage.sol#L40

```
54: uint16 txNumberInBlock;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Storage.sol#L54

```
125: uint64 expirationBlock = uint64(block.number + PRIORITY_EXPIRATION);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol#L125

```
41: uint64 blockNumber;
42: uint64 timestamp;
43: uint64 indexRepeatedStorageChanges;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L41-L43

```
24: uint16 selectorPosition;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L24

```
33: uint16 facetPosition;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L33

```
207: uint16 selectorPosition = uint16(ds.facetToSelectors[_facet].selectors.length);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L207

```
12: uint64 expirationBlock;
13: uint192 layer2Tip;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L12-L13

```
17: uint160 constant SYSTEM_CONTRACTS_OFFSET = 0x8000;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/L2ContractHelper.sol#L17

```
29: uint8 private decimals_;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L29




### <a href="#Summary">[GAS&#x2011;7]</a><a name="GAS&#x2011;7"> Optimize names to save gas

`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://github.com/enzosv/solidity-optimize-name) link for an example of how it works. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

#### <ins>Proof Of Concept</ins>

Relevant to all in-scope contracts.






### <a href="#Summary">[GAS&#x2011;8]</a><a name="GAS&#x2011;8"> Use `uint256(1)`/`uint256(2)` instead for `true` and `false` boolean states

If you don't use boolean for storage you will avoid Gwarmaccess 100 gas. In addition, state changes of boolean from `true` to `false` can cost up to ~20000 gas rather than `uint256(2)` to `uint256(1)` that would cost significantly less.

#### <ins>Proof Of Concept</ins>


```
40: mapping(uint256 => mapping(uint256 => bool)) public isWithdrawalFinalized;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L40

```
252: isWithdrawalFinalized[_l2BlockNumber][_l2MessageIndex] = true;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L252

```
37: mapping(uint256 => mapping(uint256 => bool)) public isWithdrawalFinalized;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L37

```
207: isWithdrawalFinalized[_l2BlockNumber][_l2MessageIndex] = true;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L207

```
26: mapping(address => bool) public isAccessPublic;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L26

```
30: mapping(address => mapping(address => mapping(bytes4 => bool))) public hasSpecialAccessToCall;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L30

```
40: s.validators[_validator] = true;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondInit.sol#L40

```
22: mapping(address => bool) securityCouncilMembers;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Storage.sol#L22

```
77: mapping(address => bool) validators;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Storage.sol#L77

```
68: diamondStorage.isFrozen = false;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L68

```
98: diamondStorage.isFrozen = false;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L98

```
83: diamondStorage.isFrozen = true;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L83

```
133: isSystemContextLogProcessed = true;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L133

```
72: getters.ignoreName = true;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L72

```
78: getters.ignoreSymbol = true;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L78

```
88: getters.ignoreDecimals = true;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L88


### <a href="#Summary">[GAS&#x2011;9]</a><a name="GAS&#x2011;9"> Structs can be packed into fewer storage slots

Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

#### <ins>Proof Of Concept</ins>

```
	struct AppStorage {
	    DiamondCutStorage diamondCutStorage;
	    address governor;
	    address pendingGovernor;
	    mapping(address => bool) validators;
	    Verifier verifier;
	    uint256 totalBlocksExecuted;
	    uint256 totalBlocksVerified;
	    uint256 totalBlocksCommitted;
	    mapping(uint256 => bytes32) storedBlockHashes;
	    mapping(uint256 => bytes32) l2LogsRootHashes;
	    PriorityQueue.Queue priorityQueue;
	    IAllowList allowList;
	    VerifierParams verifierParams;
	    bytes32 l2BootloaderBytecodeHash;
	    bytes32 l2DefaultAccountBytecodeHash;
	    bool zkPorterIsAvailable;
	}
```

Saving 1 slot by changing to:

```
	struct AppStorage {
	    DiamondCutStorage diamondCutStorage;
	    bool zkPorterIsAvailable; //@audit 8 bits
	    address governor; //@audit 160 bits
	    address pendingGovernor;
	    mapping(address => bool) validators;
	    Verifier verifier;
	    uint256 totalBlocksExecuted;
	    uint256 totalBlocksVerified;
	    uint256 totalBlocksCommitted;
	    mapping(uint256 => bytes32) storedBlockHashes;
	    mapping(uint256 => bytes32) l2LogsRootHashes;
	    PriorityQueue.Queue priorityQueue;
	    IAllowList allowList;
	    VerifierParams verifierParams;
	    bytes32 l2BootloaderBytecodeHash;
	    bytes32 l2DefaultAccountBytecodeHash;
	}
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Storage.sol#L69-L105

```
    struct StoredBlockInfo {
        uint64 blockNumber;
        bytes32 blockHash;
        uint64 indexRepeatedStorageChanges;
        uint256 numberOfLayer1Txs;
        bytes32 priorityOperationsHash;
        bytes32 l2LogsTreeRoot;
        uint256 timestamp;
        bytes32 commitment;
    }
```

Saving 1 slot by changing to:

```
    struct StoredBlockInfo {
        uint64 blockNumber; //@audit 64 bits
        uint64 indexRepeatedStorageChanges; //@audit 64 bits
        bytes32 blockHash;
        uint256 numberOfLayer1Txs;
        bytes32 priorityOperationsHash;
        bytes32 l2LogsTreeRoot;
        uint256 timestamp;
        bytes32 commitment;
    }
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IExecutor#L15-L24



### <a href="#Summary">[GAS&#x2011;10]</a><a name="GAS&#x2011;10"> Superfluous event fields

`block.number` and `block.timestamp` are added to the event information by default, so adding them manually will waste additional gas.

#### <ins>Proof Of Concept</ins>

```
76:		event BlockCommit(uint256 indexed blockNumber, bytes32 indexed blockHash, bytes32 indexed commitment);
```
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IExecutor#L76


```
82:		event BlockExecution(uint256 indexed blockNumber, bytes32 indexed blockHash, bytes32 indexed commitment);
```
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IExecutor#L82


### <a href="#Summary">[GAS&#x2011;11]</a><a name="GAS&#x2011;11"> Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

#### <ins>Proof Of Concept</ins>

```
12: uint256 constant L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Config.sol#L12

```
23: uint256 constant INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + 64 * 4896;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Config.sol#L23

```
26: uint256 constant REPEATED_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + 40 * 7787;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Config.sol#L26