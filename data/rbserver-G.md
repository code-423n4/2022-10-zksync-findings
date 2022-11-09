## [G-01] ARITHMETIC OPERATION THAT DOES NOT OVERFLOW CAN BE UNCHECKED
Explicitly unchecking the arithmetic operation that does not overflow by wrapping it in `unchecked {}` costs less gas than implicitly checking it. Please consider changing the following `++i` to `i = i.uncheckedInc()`, where `uncheckedInc` is from `ethereum\contracts\common\libraries\UncheckedMath.sol`.

```solidity
ethereum\contracts\zksync\libraries\Diamond.sol
  94: for (uint256 i = 0; i < facetCutsLength; ++i) {
  132: for (uint256 i = 0; i < selectorsLength; ++i) {
  153: for (uint256 i = 0; i < selectorsLength; ++i) {
  173: for (uint256 i = 0; i < selectorsLength; ++i) {
```

## [G-02] X = X + Y CAN BE USED INSTEAD OF X += Y
x = x + y costs less gas than x += y. `s.diamondCutStorage.currentProposalId += 1` can be changed to `s.diamondCutStorage.currentProposalId = s.diamondCutStorage.currentProposalId + 1` in the following `proposeDiamondCut` function.

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L22-L32
```solidity
    function proposeDiamondCut(Diamond.FacetCut[] calldata _facetCuts, address _initAddress) external onlyGovernor {
        require(s.diamondCutStorage.proposedDiamondCutTimestamp == 0, "a3"); // proposal already exists

        // NOTE: governor commits only to the `facetCuts` and `initAddress`, but not to the calldata on `initAddress` call.
        // That means the governor can call `initAddress` with ANY calldata while executing the upgrade.
        s.diamondCutStorage.proposedDiamondCutHash = keccak256(abi.encode(_facetCuts, _initAddress));
        s.diamondCutStorage.proposedDiamondCutTimestamp = block.timestamp;
        s.diamondCutStorage.currentProposalId += 1;

        emit DiamondCutProposal(_facetCuts, _initAddress);
    }
```

## [G-03] UNUSED NAMED RETURN COSTS UNNECESSARY DEPLOYMENT GAS
When a function has unused named return, unnecessary deployment gas is used. Please consider removing `storedNewBlock` that is not used in the following `_commitOneBlock` function

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L23-L78
```solidity
    function _commitOneBlock(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)
        internal
        view
        returns (StoredBlockInfo memory storedNewBlock)
    {
        ...

        return
            StoredBlockInfo(
                _newBlock.blockNumber,
                _newBlock.newStateRoot,
                _newBlock.indexRepeatedStorageChanges,
                _newBlock.numberOfLayer1Txs,
                _newBlock.priorityOperationsHash,
                _newBlock.l2LogsTreeRoot,
                _newBlock.timestamp,
                commitment
            );
    }
```

## [G-04] REDUNDANT TYPE CONVERSION CAN BE AVOIDED
Converting a variable type when not needed costs more unnecessary gas. In the following `l2TokenAddress` function, `l2TokenFactory` is already an `address` so executing `address(l2TokenFactory)` is not necessary and wastes run-time gas if this function is called by another non-view function. If this function will not be called by another non-view function, unnecessary deployment gas is still used.

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L282-L287
```solidity
    function l2TokenAddress(address _l1Token) public view returns (address) {
        bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));
        bytes32 salt = bytes32(uint256(uint160(_l1Token)));

        return L2ContractHelper.computeCreate2Address(l2Bridge, salt, l2ProxyTokenBytecodeHash, constructorInputHash);
    }
```