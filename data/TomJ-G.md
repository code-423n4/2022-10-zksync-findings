## Table of Contents
- Defined Variables Used Only Once
- Redundant Use of Variable
- Struct Member can be Packed into Fewer Storage Slots
- Unchanging State Variable Should be Immutable
- X = X + Y costs less gass than X += Y
- Use require instead of &&
- Change Function Visibility Public to External
- Internal Function Called Only Once can be Inlined
- Use Calldata instead of Memory for Read Only Function Parameters
- Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas
- Empty Blocks Should Emit Something or be Removed

&ensp;
### Defined Variables Used Only Once

#### Issue
Certain variables is defined even though they are used only once.
Remove these unnecessary variables to save gas.
For cases where it will reduce the readability, one can use comments to help describe
what the code is doing.

#### PoC
1. Remove `oldFacet` variable of _addFunctions function of Diamond.sol
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L134-L135
Mitigation:
Delete line 134 and replace line 135 with below code 
```solidity
            require(ds.selectorToFacet[selector].facetAddress == address(0), "J"); // facet for this selector already exists
```

2. Remove `selectorsLength` variable of _saveFacetIfNew function of Diamond.sol
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L187-L189
Mitigation:
Delete line 187 and replace line 189 with below code 
```solidity
        if (ds.facetToSelectors[_facet].selectors.length == 0) {
```

#### Mitigation
Don't define variable that is used only once.
Details are listed on above PoC.

&ensp;
### Redundant Use of Variable

#### Issue
Below has redundant use of variables. 
Instead of defining a new variable, emit the event first and then update the variable.

#### PoC
Total of 2 instances found.
1. acceptGovernor function of Governance.sol
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Governance.sol#L28-L40
Change it to
```solidity
    function acceptGovernor() external {
        address pendingGovernor = s.pendingGovernor;
        require(msg.sender == pendingGovernor, "n4"); // Only proposed by current governor address can claim the governor rights

        if (pendingGovernor != s.governor) {
            emit NewPendingGovernor(pendingGovernor, address(0));
            emit NewGovernor(s.governor, pendingGovernor);

            s.governor = pendingGovernor;
            delete s.pendingGovernor;
        }
    }
```

2. setVerifierParams function of Governance.sol
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Governance.sol#L104-L109
Change it to
```solidity
    function setVerifierParams(VerifierParams calldata _newVerifierParams) external onlyGovernor {
        emit NewVerifierParams(s.verifierParams, _newVerifierParams);

        s.verifierParams = _newVerifierParams;
    }
```

#### Mitigation
Instead of defining a new variable, emit the event first and then update the variable like shown in above PoC.

&ensp;
### Struct Member can be Packed into Fewer Storage Slots

#### Issue
The order of struct member can be reordered in a way to reduce the usage amount of storage slots.
```
Reference from solidity documentation:
Finally, in order to allow the EVM to optimize for this, ensure that you try to order your storage 
variables and struct members such that they can be packed tightly. For example, declaring your 
storage variables in the order of uint128, uint128, uint256 instead of uint128, uint256, uint128, 
as the former will only take up two slots of storage whereas the latter will take up three.
```
https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html#layout-of-state-variables-in-storage

#### PoC
Total of 2 instances found.

1. struct `StoredBlockInfo` of IExecutor.sol
Originally it uses 8 slots but this can be reduced to 7 slots by reordering the struct member like below.
Switch `indexRepeatedStorageChanges`(8 bytes) and `blockHash`(32 bytes) order, 
so that `blockNumber`(8 bytes) and `indexRepeatedStorageChanges` can fit into 1 slot.
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IExecutor.sol#L17-L18
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

2. struct `AppStorage` of Storage.sol
Originally it uses 16 slots but this can be reduced to 15 slots by reordering the struct member like below.
Move bool variable `zkPorterIsAvailable`(1 bytes) to bottom of address varialbe `pendingGovernor`(20 bytes), 
so that these 2 variable can be packed into 1 slot.
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/Storage.sol#L69-L105
```solidity
struct AppStorage {
    DiamondCutStorage diamondCutStorage;
    address governor;
    address pendingGovernor;
    bool zkPorterIsAvailable;
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

#### Mitigation
Reorder struct member like shown in above PoC.

&ensp;
### X = X + Y costs less gass than X += Y

#### Issue
X = X + Y costs less gass than X += Y for state variables

#### PoC
Total of 1 instance found.
```solidity
./DiamondCut.sol:29:        s.diamondCutStorage.currentProposalId += 1;
```

&ensp;
### Unchanging State Variable Should be Immutable

#### Issue
State variable that is only set in the constructor and can't be changed afterwards,
should be declared as immutable.

#### PoC
Total of 5 instances found.

1. l2Bridge variable of L1ERC20Bridge.sol
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L47

2. l2TokenFactory variable of L1ERC20Bridge.sol
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L50

3. l2ProxyTokenBytecodeHash variable of L1ERC20Bridge.sol
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L53

4. l2Bridge variable of L1EthBridge.sol
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L44

5. l2TokenFactory variable of L2ERC20Bridge.sol
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ERC20Bridge.sol#L23

#### Mitigation
Change to immutable.

&ensp;
### Use require instead of &&

#### Issue
When there are multiple conditions in require statement, break down the require statement into
multiple require statements instead of using && can save gas.

#### PoC
```
./ethereum_L2ContractHelper.sol:65:        require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
```

#### Mitigation
Break down into several require statement.

&ensp;
### Change Function Visibility Public to External

#### Issue
If the function is not called internally, it is cheaper to set your function visibility to external instead of public.

#### PoC
Total of 1 instance found.

Mailbox.sol:l2TransactionBaseCost()
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Mailbox.sol#L89

#### Mitigation
Change the function visibility to external.

&ensp;
### Internal Function Called Only Once Can be Inlined

#### Issue
Certain function is defined even though it is called only once.
Inline it instead to where it is called to avoid usage of extra gas.

#### PoC
Total of 21 instances found.

1. uncheckedAdd() of UncheckedMath.sol
This function called only once at line 111 of Executor.sol
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/libraries/UncheckedMath.sol#L12
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L111

2. _L2MessageToLog() of Mailbox.sol
This function called only once at line 32
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Mailbox.sol#L75
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Mailbox.sol#L32

3. _requestL2Transaction() of Mailbox.sol
This function called only once at line 113
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Mailbox.sol#L116
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Mailbox.sol#L113

4. _writePriorityOp() of Mailbox.sol
This function called only once at line 131
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Mailbox.sol#L144
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Mailbox.sol#L131

5. calculateRoot() of Merkle.sol
This function called only once at line 68 of Mailbox.sol
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Merkle.sol#L17
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Mailbox.sol#L68

6. _depositFunds() of L1ERC20Bridge.sol
This function called only once at line 116
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L136
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L116

7. _getERC20Getters() of L1ERC20Bridge.sol
This function called only once at line 155
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L164
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L155

8. getSize() of PriorityQueue.sol
This function called only once at line 65 of Getters.sol
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L44
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Getters.sol#L65

9. pushBack() of PriorityQueue.sol
This function called only once at line 165 of Mailbox.sol
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L54
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Mailbox.sol#L165

10. front() of PriorityQueue.sol
This function called only once at line 70 of Getters.sol
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L63
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Getters.sol#L70

11. popFront() of PriorityQueue.sol
This function called only once at line 181 of Executor.sol
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L71
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L181

12. _deployL2Token() of L2ERC20Bridge.sol
This function called only once at line 62
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ERC20Bridge.sol#L73
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ERC20Bridge.sol#L62

13. _collectOperationsFromPriorityQueue() of Executor.sol
This function called only once at line 198
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L177
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L198

14. _executeOneBlock() of Executor.sol
This function called only once at line 211
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L190
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L211

15. _getBlockProofPublicInput() of Executor.sol
This function called only once at line 248
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L274
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L248

16. _verifyRecursivePartOfProof() of Executor.sol
This function called only once at line 264
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L296
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L264

17. _maxU256() of Executor.sol
This function called only once at line 338
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L349
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L338

18. _createBlockCommitment() of Executor.sol
This function called only once at line 65
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L354
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L65

19. _blockPassThroughData() of Executor.sol
This function called only once at line 355
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L362
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L355

20. _blockMetaParameters() of Executor.sol
This function called only once at line 356
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L372
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L356

21. _blockAuxilaryOutput() of Executor.sol
This function called only once at line 357
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L376
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L357

#### Mitigation
I recommend to not define above functions and instead inline it at place it is called.

&ensp;
### Use Calldata instead of Memory for Read Only Function Parameters

#### Issue
It is cheaper gas to use calldata than memory if the function parameter is read only.
Calldata is a non-modifiable, non-persistent area where function arguments are stored, 
and behaves mostly like memory. More details on following link.
link: https://docs.soliditylang.org/en/v0.8.15/types.html#data-location

#### PoC
Total of 5 instances found.
```
./ExternalDecoder.sol:10:    function decodeString(bytes memory _input) external pure returns (string memory result) {
./ExternalDecoder.sol:15:    function decodeUint8(bytes memory _input) external pure returns (uint8 result) {
./Mailbox.sol:43:        L2Log memory _log,
./L2StandardERC20.sol:43:    function bridgeInitialize(address _l1Address, bytes memory _data) external initializer {
./Executor.sol:152:    function commitBlocks(StoredBlockInfo memory _lastCommittedBlockData, CommitBlockInfo[] calldata _newBlocksData)
```

#### Mitigation
Change memory to calldata

&ensp;
### Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas

#### Issue
Since EVM operates on 32 bytes at a time, if the element is smaller than that, the EVM must use more operations
in order to reduce the elements from 32 bytes to specified size. Therefore it is more gas saving to use 32 bytes 
unless it is used in a struct or state variable in order to reduce storage slot. 

Reference: https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html

#### PoC
Total of 13 instances found.
```
./DiamondInit.sol:30:        uint64 _genesisIndexRepeatedStorageChanges,
./Mailbox.sol:92:        uint32 // _calldataLength
./Mailbox.sol:125:        uint64 expirationBlock = uint64(block.number + PRIORITY_EXPIRATION);
./Mailbox.sol:150:        uint64 _expirationBlock,
./Diamond.sol:207:        uint16 selectorPosition = uint16(ds.facetToSelectors[_facet].selectors.length);
./L1ERC20Bridge.sol:185:        uint16 _l2TxNumberInBlock,
./L1ERC20Bridge.sol:228:        uint16 _l2TxNumberInBlock,
./L2StandardERC20.sol:12:    event BridgeInitialization(address indexed l1Token, string name, string symbol, uint8 decimals);
./L2StandardERC20.sol:29:    uint8 private decimals_;
./L2StandardERC20.sol:126:    function decimals() public view override returns (uint8) {
./L1EthBridge.sol:138:        uint16 _l2TxNumberInBlock,
./L1EthBridge.sol:185:        uint16 _l2TxNumberInBlock,
./ethereum/contracts/common/L2ContractHelper.sol:64:        uint8 version = uint8(_bytecodeHash[0]);
```

#### Mitigation
I suggest using uint256 instead of anything smaller and downcast where needed.

&ensp;
### Empty Blocks Should Emit Something or be Removed

#### Issue
There are function with empty blocks. These should do something like emit an event or reverting.
If not it should be removed from the contract.

#### PoC
Total of 2 found.

1. Save 23877 gas by deleting empty constructor() of DiamondInit.sol
Before gas avg: 293178
After gas avg: 269301
```solidity
DiamondInit.sol:15:    constructor() reentrancyGuardInitializer {}
```

2. Delete IZkySync.sol contract since it is empty and unnecessary contract.
```solidity
IZkSync.sol:11:interface IZkSync is IMailbox, IGovernance, IExecutor, IDiamondCut, IGetters {}
```

&ensp;