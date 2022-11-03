## Gas Report 2022-10-zksync

Three gas optimisations in two areas were found other than the [C4udit](https://gist.github.com/Picodes/1f87a82e954cc749dea9d9961d5f4dff) public findings.
- [Use require() over assert()](#use-require-over-assert)
- [Storage Optimisation](#storage-optimisation)

### Use require over assert
Assert should not be used by other mean than testing purposes. (because when the assertion fails, gas is NOT refunded contrary to require)

[DiamondCut.sol](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/DiamondCut.sol#L16)
```solidity
    assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);
```

### Storage Optimisation
Changing order of storage slots 20 000 gas saved at deployment per slot saved. Ethereum storage is composed of slots of 32 bytes, the problem is that writing is expensive. (Up to 20000gas using “cold” writing) https://docs.soliditylang.org/en/v0.8.13/internals/layout_in_storage.html

### Affected contracts
- ./ethereum/contracts/zksync/Storage.sol
- ./ethereum/contracts/zksync/interfaces/IExecutor.sol

### Storage.sol
Potential for contract storage optimisation in [Storage.sol](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Storage.sol#L69-L105) reducing storage slots required by 1. Variable zkPorterIsAvailable is of type boolean taking up 1 byte of storage and 1 slot of storage at the end of the struct as the previous entity of the struct is bytes32 which fill an entire storage slot. Variable zkPorterIsAvailable can be put together with an address entity of size bytes 20 to share a single slot utilising 21 of 32 bytes rather than currently having a slot occupying 1 of 32 bytes.

#### Original
(@dev comments removed to save space)
```solidity
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
| size    | slot    | type    |
|:-------:|:-------:| -------:|
| 32      | 0       | struct  |
| 20      | 1       | address |
| 20      | 2       | address |
| 32      | 3       | mapping |
| 20      | 4       | address |
| 32      | 5       | uint256 |
| 32      | 6       | uint256 |
| 32      | 7       | uint256 |
| 32      | 8       | mapping |
| 32      | 9       | mapping |
| 32      | 10      | struct  |
| 32      | 11      | address |
| 32      | 12      | struct  |
| 32      | 13      | bytes32 |
| 32      | 14      | bytes32 |
| 1       | 15      | bool    |

#### Modified
(@dev comments removed to save space)
```solidity
struct AppStorage {
    DiamondCutStorage diamondCutStorage;
    bool zkPorterIsAvailable;
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
}
```
| size    | slot    | type    |
|:-------:|:-------:| -------:|
| 32      | 0       | struct  |
| 1       | 1       | bool    |
| 20      | 1       | address |
| 20      | 2       | address |
| 32      | 3       | mapping |
| 20      | 4       | address |
| 32      | 5       | uint256 |
| 32      | 6       | uint256 |
| 32      | 7       | uint256 |
| 32      | 8       | mapping |
| 32      | 9       | mapping |
| 32      | 10      | struct  |
| 32      | 11      | address |
| 32      | 12      | struct  |
| 32      | 13      | bytes32 |
| 32      | 14      | bytes32 |

### IExecutor.sol
There may be potential for contract storage optimisation in [IExecutor.sol](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IExecutor.sol#L15-L24) reducing storage slots required by 1. As the struct contains rollup block stored data, this may be a fixed struct data format reliant on how the struct data is populated invalidating this optimisation, however seems to be able to be modified for optimisation with data coming from [DiamondInit.sol](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/DiamondInit.sol) and [Executor.sol](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol).
Variables blockNumber and indexRepeatedStorageChanges are both of type uint64 taking up 8 bytes each of storage and one slot of storage each. Having variable blockHash of type bytes32 in between the two uint64 variables taking up an entire storage slot. Should it be possible to position both blockHash and indexRepeatedStorageChanges together, they will have a combined size of 16 bytes enabling them to share a single storage slot utilising 16 of 32 bytes rather than currently having one slot each.

#### Original
```solidity
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
| size    | slot    | type    |
|:-------:|:-------:| -------:|
| 8       | 0       | uint64  |
| 32      | 1       | bytes32 |
| 8       | 2       | uint64  |
| 32      | 3       | uint256 |
| 32      | 4       | bytes32 |
| 32      | 5       | bytes32 |
| 32      | 6       | uint256 |
| 32      | 7       | bytes32 |

#### Modified
```solidity
    struct StoredBlockInfo {
        uint64 blockNumber;
        uint64 indexRepeatedStorageChanges;
        bytes32 blockHash;
        uint256 numberOfLayer1Txs;
        bytes32 priorityOperationsHash;
        bytes32 l2LogsTreeRoot;
        uint256 timestamp;
        bytes32 commitment;
    }
```
| size    | slot    | type    |
|:-------:|:-------:| -------:|
| 8       | 0       | uint64  |
| 8       | 0       | uint64  |
| 32      | 1       | bytes32 |
| 32      | 2       | uint256 |
| 32      | 3       | bytes32 |
| 32      | 4       | bytes32 |
| 32      | 5       | uint256 |
| 32      | 6       | bytes32 |
