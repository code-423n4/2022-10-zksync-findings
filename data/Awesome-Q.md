## `block.timestamp` is unreliable

Using block.timestamp as part of the time checks could be slightly modified by miners/validators to favor them in contracts that contain logic heavily dependent on them.

Consider this problem and warn users that a scenario like this could occur. If the change of timestamps will not affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future.

Here are some of the instances:
[Line 28](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L28)

[Line 52](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L52)

[Line 85](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L85)

[Line 50-51](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L50-L51)

## Typos

Consider sticking to the proper spelling of words otherwise, it will decrease readability

There are 6 instances of this issue
consider making the following changes:

[Line 36](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L36)

```
Line 36:    emit DiamondCutProposalCancelation(
```

Refactor to

```
Line 36:    emit DiamondCutProposalCancellation(
```

[Line 30](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L30), [Line 296](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L296), [Line 357](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L357), [Line 376](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol)

```
Line 30:    // Check that block contain all meta information for L2 logs.

Line 296:    function _verifyRecursivePartOfProof(uint256[] calldata _recurisiveAggregationInput) internal view returns (bool) {

Line 357:    bytes32 auxiliaryOutputHash = keccak256(_blockAuxilaryOutput(_newBlockData));

Line 376:    function _blockAuxilaryOutput(CommitBlockInfo calldata _block) internal pure returns (bytes memory) {
```

Refactor to

```
Line 30:    // Check that block contains all meta information for L2 logs.

Line 296:    function _verifyRecursivePartOfProof(uint256[] calldata _recursiveAggregationInput) internal view returns (bool) {

Line 357:    bytes32 auxiliaryOutputHash = keccak256(_blockAuxiliaryOutput(_newBlockData));

Line 376:    function _blockAuxiliaryOutput(CommitBlockInfo calldata _block) internal pure returns (bytes memory) {
```

## Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

For example:
🤦 Bad:

```
pragma solidity ^0.8.0;
```

🚀 Good:

```
pragma solidity 0.8.4;
```

for more info:
[Consensys Audit of 1inch](https://consensys.net/diligence/audits/2020/12/1inch-liquidity-protocol/#unspecific-compiler-version-pragma)

[Solidity docs](https://docs.soliditylang.org/en/latest/layout-of-source-files.html#version-pragma)
