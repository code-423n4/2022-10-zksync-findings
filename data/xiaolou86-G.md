1. Don’t compare boolean expressions to boolean literals.

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol#L221

2. ++i should be unchecked{++i} when it is not possible for them to overflow, as is the case when used in for- and while-loops.
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L94
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L132
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L153
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L173

3. <x> += <y> costs more gas than <x> = <x> + <y>
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L29
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/TranscriptLib.sol#L44



