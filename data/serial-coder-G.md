# \[G-01\] Optimizing The Struct StoredBlockInfo To Save Storage Slots

## Gas Issue Details

**The struct `StoredBlockInfo` (code snippet 1) would be used to store all committed blocks** in the zkSync platform. In the current implementation, **the struct would take 8 storage slots for each committed block**.

```solidity
SNIPPET: 1
FILE: https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol

15:     struct StoredBlockInfo {
16:         uint64 blockNumber;
17:         bytes32 blockHash;
18:         uint64 indexRepeatedStorageChanges;
19:         uint256 numberOfLayer1Txs;
20:         bytes32 priorityOperationsHash;
21:         bytes32 l2LogsTreeRoot;
22:         uint256 timestamp;
23:         bytes32 commitment;
24:     }
```

## Proof of Concept

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L16-L18

## Gas Improvements

We discovered that **the struct `StoredBlockInfo` could be optimized by relocating the struct properties `blockNumber` and `indexRepeatedStorageChanges` to be adjacent**, like L17 and L18 in code snippet 2.

**The optimized struct would save 1 storage slot because the properties `blockNumber` and `indexRepeatedStorageChanges` could be stored in the same storage slot.**

This optimization would **save a lot of storage slots (gas)** since the optimized struct would be used to store every committed block.

```solidity
SNIPPET: 2
FILE: https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol

15:     struct StoredBlockInfo {
16:         bytes32 blockHash;
17:         uint64 blockNumber;
18:         uint64 indexRepeatedStorageChanges;
19:         uint256 numberOfLayer1Txs;
20:         bytes32 priorityOperationsHash;
21:         bytes32 l2LogsTreeRoot;
22:         uint256 timestamp;
23:         bytes32 commitment;
24:     }
```



---



# \[G-02\] Removing Unused State Variables

## Gas Issue Details

We detected the following **two state variables were declared but never used** in the `L2ETHBridge` contract.

1. **`totalSupply` variable (L16 in code snippet 1)**
2. **`balanceOf` variable (L19 in code snippet 1)**

```solidity
SNIPPET: 1
FILE: https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol

14:     contract L2ETHBridge is IL2Bridge {
15:         /// @dev The total amount of tokens that have been minted
16:         uint256 public totalSupply;
17: 
18:         /// @dev Mapping of address to the balance
19:         mapping(address => uint256) public balanceOf;

            // (...SNIPPED...)
87:     }
```

## Proof of Concept

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L16

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L19

## Gas Improvements

We recommend **removing the unused state variables `totalSupply` and `balanceOf`** from the `L2ETHBridge` contract to save 2 storage slots.



---



# \[G-03\] Removing The Process Of Cleaning Up Processed Priority Operations

## Gas Issue Details

The `popFront` function (code snippet 1) of the `PriorityQueue` library cleans up the priority operation pointed by the queue's head (L78) and then increases the queue's head to the next unprocessed priority operation in the queue (L79).

We noticed that **the process of cleaning up the priority operation in L78 would unnecessarily spend gas**.

Furthermore, **cleaning up all processed priority operations could impede the users or developers from tracing the processed priority operations later, leading to transparency and traceability issues**.

```solidity
SNIPPET: 1
FILE: https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol

71:     function popFront(Queue storage _queue) internal returns (PriorityOperation memory priorityOperation) {
72:         require(!_queue.isEmpty(), "s"); // priority queue is empty
73: 
74:         // Save value into the stack to avoid double reading from the storage
75:         uint256 head = _queue.head;
76: 
77:         priorityOperation = _queue.data[head];
78:         delete _queue.data[head];
79:         _queue.head = head + 1;
80:     }
```

## Proof of Concept

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L78

## Gas Improvements

We recommend **removing the process of cleaning up the priority operation** (L78 in the code snippet 1 above) to save gas, like the code snippet 2 below.

Moreover, we considered that **maintaining all processed priority operations would improve the transparency and traceability of the zkSync platform**.

```solidity
SNIPPET: 2
FILE: https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol

71:     function popFront(Queue storage _queue) internal returns (PriorityOperation memory priorityOperation) {
72:         require(!_queue.isEmpty(), "s"); // priority queue is empty
73: 
74:         // Save value into the stack to avoid double reading from the storage
75:         uint256 head = _queue.head;
76: 
77:         priorityOperation = _queue.data[head];
78:         _queue.head = head + 1;
79:     }
```