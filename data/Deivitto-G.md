
# GAS
## duplicated `require()` check should be refactored
### Summary
duplicated `require()` / `revert()` checks should be refactored to a modifier or function to save gas
### Details
Event appears twice and can be reduced

### Github Permalinks
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L80
        require(!diamondStorage.isFrozen, "a9"); // diamond proxy is frozen already

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L106
        require(s.diamondCutStorage.securityCouncilMembers[msg.sender], "a9"); // not a security council member

### Mitigation
refactor this checks to different functions to save gas


## splitting `require()` statements that use `&&` saves gas
### Summary
Instead of using the && operator in a single require statement to check multiple conditions, consider using multiple require statements with 1 condition per require statement (saving 3 gas per & ):
### Github Permalinks


https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L97
            callersLength == _targets.length &&

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L98
                callersLength == _functionSigs.length &&

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/L2ContractHelper.sol#L65
        require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash



### Mitigation
Split require statements



## usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
### Summary
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

### Details
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html 
Use a larger size than downcast where needed

### Github Permalinks
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L29
    uint8 private decimals_;

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L29
    uint8 private decimals_;





### Mitigation
Consider using some data type that uses 32 bytes, for example uint256


## Store using Struct over multiple mappings
### Summary
All these variables could be combine in a Struct in order to reduce the gas cost. 
### Details
As noticed in: 
https://gist.github.com/alexon1234/b101e3ac51bea3cbd9cf06f80eaa5bc2
When multiple mappings that access the same addresses, uints, etc, all of them can be mixed into an struct and then that data accessed like:
mapping(datatype => newStructCreated) newStructMap;
Also, you have this post where it explains the benefits of using Structs over mappings 
https://medium.com/@novablitz/storing-structs-is-costing-you-gas-774da988895e

### Github Permalinks
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Storage.sol#L22
    mapping(address => bool) securityCouncilMembers;

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Storage.sol#L23
    mapping(address => uint256) securityCouncilMemberLastApprovedProposalId;

- - - - -
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L26
    mapping(address => bool) public isAccessPublic;

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L30
    mapping(address => mapping(address => mapping(bytes4 => bool))) public hasSpecialAccessToCall;

- - - - - 

### Mitigation
Consider mixing different mappings into an struct when able in order to save gas.


## Pack structs tightly
### Summary
Gas efficiency can be achieved by tightly packing the struct. Struct variables are stored in 32 bytes each so you can group smaller types to occupy less storage. 

### Details
You can read more here: https://fravoll.github.io/solidity-patterns/tight_variable_packing.html or in the official documentation: https://docs.soliditylang.org/en/v0.4.21/miscellaneous.html
### Github Permalinks
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IExecutor.sol#L15
    struct StoredBlockInfo {


https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IMailbox.sol#L24
    struct L2CanonicalTransaction {

### Mitigation
Order structs to reduce gas usage.



## Variables should be cached when used several times
### Summary
Variables read more than once improves gas usage when cached into local variable
### Details
In loops or state variables, this is even more gas saving

### Github Permalinks
_blocksData[i]
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L211-L213

facetCuts[i]
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L95-L98

### Mitigation
Cache variables used more than one into a local variable.






## Functions guaranteed to revert when called by normal users can be marked `payable`
### Summary
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function.

Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

### Details
The extra opcodes avoided are:
CALLVALUE (2), DUP1 (3), ISZERO (3), PUSH2 (3), JUMPI (10), PUSH1 (3), DUP1 (3), REVERT(0), JUMPDEST (1), POP (2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

### Github Permalinks

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L57
    function setPublicAccess(address _target, bool _enable) external onlyOwner {

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L65
    function setBatchPublicAccess(address[] calldata _targets, bool[] calldata _enables) external onlyOwner {

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L94
    ) external onlyOwner {

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L118
    ) external onlyOwner {

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L140
    function setPendingOwner(address _newPendingOwner) external onlyOwner {

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L38
        require(msg.sender == owner, "kx");


### Mitigation
Consider adding payable to save gas


## `>=` cheaper than `>`
### Summary
Strict inequalities ( `>` ) are more expensive than non-strict ones ( `>=` ). This is due to some supplementary checks (`ISZERO`, 3 gas)

### Github Permalinks
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L350
### Mitigation
Consider using `return b >= a? a: b` instead of `return a < b ? b : a` to avoid some opcodes

## `<X> += <Y>` costs more gas than `<X> = <X> + <Y>` for state variables
### Summary
`x+=y` costs more gas than x=x+y for state variables

### Github Permalinks


https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L29
        s.diamondCutStorage.currentProposalId += 1;

### Mitigation
Don't use `+=` for state variables as it cost more gas.


## Unused named returns
### Summary
Using both named returns and a return statement isn’t necessary. Removing one of those can improve code clarity 
### Details
Also as returns variable is ignored, it wastes extra gas

### Github Permalinks
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L23-L78

### Mitigation
Remove return or returns when both used

## `abi.encode()` is less gas efficient than `abi.encodePacked()`
### Summary
In general, `abi.encodePacked` is more gas-efficient.

### Details
Changing the abi.encode function to `abi.encodePacked` can save gas since the abi.encode function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, `abi.encodePacked` is more gas-efficient.
### Github Permalinks

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L85
        return keccak256(abi.encode(_previousBlock.blockHash, _newBlock.newStateRoot));

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L122
                chainedPriorityTxsHash = keccak256(abi.encode(chainedPriorityTxsHash, canonicalTxHash));

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L182
            concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L359
        return keccak256(abi.encode(passThroughDataHash, metadataHash, auxiliaryOutputHash));

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L381
        return abi.encode(_block.l2LogsTreeRoot, l2ToL1LogsHash, initialStorageChangesHash, repeatedStorageChangesHash);

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L386
        return keccak256(abi.encode(_storedBlockInfo));

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Merkle.sol#L30
                currentHash = keccak256(abi.encode(currentHash, _path[i]));

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Merkle.sol#L32
                currentHash = keccak256(abi.encode(_path[i], currentHash));

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L82
        bytes memory create2Input = abi.encode(address(this), l2ProxyTokenBytecodeHash, _governor);

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L168
        data = abi.encode(data1, data2, data3);

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L283
        bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L58
        bytes memory create2Input = abi.encode(address(this));


https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ERC20Bridge.sol#L114
        bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));


### Mitigation
Consider changing abi.encode to `abi.encodePacked`


## Internal functions only called once can be inlined to save gas
### Summary
Not inlining costs `20` to `40` gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.
### Github Permalinks


https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L177
    function _collectOperationsFromPriorityQueue(uint256 _nPriorityOps) internal returns (bytes32 concatHash) {

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L190
    function _executeOneBlock(StoredBlockInfo memory _storedBlock, uint256 _executedBlockIdx) internal {

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L279
    ) internal pure returns (uint256) {

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L296
    function _verifyRecursivePartOfProof(uint256[] calldata _recurisiveAggregationInput) internal view returns (bool) {

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L349
    function _maxU256(uint256 a, uint256 b) internal pure returns (uint256) {

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L354
    function _createBlockCommitment(CommitBlockInfo calldata _newBlockData) internal view returns (bytes32) {

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L362
    function _blockPassThroughData(CommitBlockInfo calldata _block) internal pure returns (bytes memory) {

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L372
    function _blockMetaParameters() internal view returns (bytes memory) {

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L376
    function _blockAuxilaryOutput(CommitBlockInfo calldata _block) internal pure returns (bytes memory) {


https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L75
    function _L2MessageToLog(L2Message calldata _message) internal pure returns (L2Log memory) {

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L164
    function _getERC20Getters(address _token) internal view returns (bytes memory data) {

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ERC20Bridge.sol#L73
    function _deployL2Token(address _l1Token, bytes calldata _data) internal returns (address) {


### Mitigation
Consider changing internal function only called once to inline code for gas savings

## Unnecesary storage read
### Summary
A value which value is already known can be used directly rather than reading it from the storage
### Example
```
    function revertBlocks(uint256 _newLastBlock) external nonReentrant onlyValidator {
        require(s.totalBlocksCommitted > _newLastBlock, "v1"); // the last committed block is less new last block
        uint256 newTotalBlocksCommitted = _maxU256(_newLastBlock, s.totalBlocksExecuted);

        if (newTotalBlocksCommitted < s.totalBlocksVerified) {
            s.totalBlocksVerified = newTotalBlocksCommitted;
        }
        s.totalBlocksCommitted = newTotalBlocksCommitted;

        emit BlocksRevert(s.totalBlocksCommitted, s.totalBlocksVerified, s.totalBlocksExecuted);
    }
```

Recommendation
Change to:

```
    function revertBlocks(uint256 _newLastBlock) external nonReentrant onlyValidator {
        require(s.totalBlocksCommitted > _newLastBlock, "v1"); // the last committed block is less new last block
        uint blocksExecuted = s.totalBlocksExecuted;
        uint256 newTotalBlocksCommitted = _maxU256(_newLastBlock, blocksExecuted);

        if (newTotalBlocksCommitted < s.totalBlocksVerified) {
            s.totalBlocksVerified = newTotalBlocksCommitted;
        }
        s.totalBlocksCommitted = newTotalBlocksCommitted;

        emit BlocksRevert(newTotalBlocksCommitted, s.totalBlocksVerified, blocksExecuted);//@audit avoiding extra reads
    }
```



### Github Permalinks
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L345


### Mitigation
Set directly the value to avoid unnecessary storage read to save some gas


## Functions can revert rather than using a conditional in order to save gas
### Impact
Functions that only has a path with a conditional can revert rather than using a `if` in order to save the gas of the call when condition is not accomplished

### Example
```
    /// @notice Change validator status (active or not active)
    /// @param _validator Validator address
    /// @param _active Active flag
    function setValidator(address _validator, bool _active) external onlyGovernor {
        if (s.validators[_validator] != _active) {
            s.validators[_validator] = _active;
            emit ValidatorStatusUpdate(_validator, _active);
        }
    }
```

Can be

```
    /// @notice Change validator status (active or not active)
    /// @param _validator Validator address
    /// @param _active Active flag
    function setValidator(address _validator, bool _active) external onlyGovernor {
        require (s.validators[_validator] != _active);
        s.validators[_validator] = _active;
        emit ValidatorStatusUpdate(_validator, _active);
    }
```

### Github permalinks
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Governance.sol#L45-L50
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Governance.sol#L84-L100
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Governance.sol#L15-L40
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L140-L164
### Mitigation
Consider using `require` / `revert` in this cases for saving gas of the call when condition is not met

