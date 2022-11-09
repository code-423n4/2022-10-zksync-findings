# QA

## [N-01] Incomplete Natspec

Some function parameters are not described in Natspec comments in the `initialize` function.

[DiamondInit.sol#L25](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/DiamondInit.sol#L25)

```solidity
    function initialize(
```

## [N-02] Typo in `Executor.sol`

The input variable `_recurisiveAggregationInput` may have been intended to be called `_recursiveAggregationInput`.

[Executor.sol#L296](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L296)

```solidity
    function _verifyRecursivePartOfProof(uint256[] calldata _recurisiveAggregationInput) internal view returns (bool) {
```

## [N-03] Dead code: `_calculateBlockHash` is not used in `Executor.sol`

The function `Executor._calculateBlockHash` is internal and is not used in any of the contracts. The code should be removed or the visibility should be changed to external.

[Executor.sol#L80-L86](https://github.com/code-423n4/2022-10-zksync/blob/358f38736351a8a27e325dfcb665eeba5ec02bd5/ethereum/contracts/zksync/facets/Executor.sol#L80-L86)

```solidity
    function _calculateBlockHash(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)
        internal
        pure
        returns (bytes32)
    {
        return keccak256(abi.encode(_previousBlock.blockHash, _newBlock.newStateRoot));
    }
```