## TABLE OF CONTENTS

- [N-01] Require() should be used instead of assert()
- [N-02] Non-library/interface files should use fixed compiler versions, not floating ones
- [N-03] Open Todos

## [N-01] Require() should be used instead of assert()

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as require()/revert()do. assert()should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/DiamondCut.sol#L16

## [N-02] Non-library/interface files should use fixed compiler versions, not floating ones

For example: 
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/DiamondCut.sol#L3

## [N-03] Open Todos

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol#L485