# Report
## Gas Optimizations ##
### [G-1]: The increment in for loop post condition can be made unchecked
**Context:**

1. ```for (uint256 i = 0; i < facetCutsLength; ++i) {``` [L94](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L94) 
1. ```for (uint256 i = 0; i < selectorsLength; ++i) {``` [L132](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L132) 
1. ```for (uint256 i = 0; i < selectorsLength; ++i) {``` [L153](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L153) 
1. ```for (uint256 i = 0; i < selectorsLength; ++i) {``` [L173](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L173) 

**Description:**

[This](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked) can save 30-40 gas per loop iteration.

**Recommendation:**

Example how to fix. Change:
```
for (uint256 i = 0; i < orders.length; ++i) {
   // Do the thing
}
```

To:
```
for (uint256 i = 0; i < orders.length;) {
   // Do the thing
   unchecked { ++i; }
}
```

### [G-2]: If internal function called only once it can be inlined to save gas
**Context:**

1. [Executor. _getBlockProofPublicInput](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L274) 
1. [Executor._maxU256](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L349) 
1. [Executor._blockPassThroughData](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L362) 
1. [Executor._blockMetaParameters](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L372) 
1. [Executor._blockAuxilaryOutput](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L376) 
1. [Mailbox._L2MessageToLog](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L75) 
1. [L1EthBridge._getDepositL2Calldata](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L113) 
1. [L2ContractHelper.bytecodeLen](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L71)

**Description:**

If [they](https://blog.soliditylang.org/2021/03/02/saving-gas-with-simple-inliner/) are not inlined, they cost an additional 20 to 40 gas because of 2 extra jump instructions and additional stack operations needed for function calls.


### [G-3]: No need to cache state variable in local memory if it only use once
**Context:**

1. ```bool isEnabled = isAccessPublic[_target];``` [L76](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L76) 
1. ```bool currentPermission = hasSpecialAccessToCall[_caller][_target][_functionSig];``` [L129](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L129) 

**Recommendation:**

Read the state variable from storage.

### [G-4]: Use calldata instead of memory
**Context:**

1. ```function _commitOneBlock(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)``` [L23](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L23) (_previousBlock)
1. ```function _calculateBlockHash(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)``` [L80](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L80) (_previousBlock)
1. ```function _executeOneBlock(StoredBlockInfo memory _storedBlock, uint256 _executedBlockIdx) internal {``` [L190](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L190) (_storedBlock)
1. ```VerifierParams memory _verifierParams``` [L278](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L278) 
1. ```function _hashStoredBlockInfo(StoredBlockInfo memory _storedBlockInfo) internal pure returns (bytes32) {``` [L385](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L385) 
1. ```L2Log memory _log,``` [L43](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L43) 
1. ```L2Log memory _log,``` [L53](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L53) 
1. ```function diamondCut(DiamondCutData memory _diamondCut) internal {``` [L89](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L89) 
1. ```bytes4[] memory _selectors,``` [L121](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L121) 
1. ```bytes4[] memory _selectors,``` [L145](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L145) 
1. ```function _removeFunctions(address _facet, bytes4[] memory _selectors) private {``` [L167](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L167) (_selectors)
1. ```function _initializeDiamondCut(address _init, bytes memory _calldata) private {``` [L277](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L277) (_calldata)
1. ```function pushBack(Queue storage _queue, PriorityOperation memory _operation) internal {``` [L54](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L54) (_operation)
1. ```function _parseL2WithdrawalMessage(bytes memory _l2ToL1message)``` [L260](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L260) 
1. ```function _parseL2WithdrawalMessage(bytes memory _message)``` [L214](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L214) 
1. ```function sendMessageToL1(bytes memory _message) internal returns (bytes32) {``` [L43](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L43) 
1. ```function hashL2Bytecode(bytes memory _bytecode) internal pure returns (bytes32 hashedBytecode) {``` [L47](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L47) 
1. ```function bridgeInitialize(address _l1Address, bytes memory _data) external initializer {``` [L43](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L43) (_data)
1. ```function decodeString(bytes memory _input) external pure returns (string memory result) {``` [L10](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/ExternalDecoder.sol#L10) 
1. ```function decodeUint8(bytes memory _input) external pure returns (uint8 result) {``` [L15](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/ExternalDecoder.sol#L15) 

**Description:**

If a reference type function parameter is read-only, it is recommended to use calldata instead of memory because this provides significant gas savings. Since Solidity v0.6.9, memory and calldata are allowed in all functions regardless of their visibility type (ie external, public, etc).
