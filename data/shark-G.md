## `uint8` can sometimes be more expensive than `uint256`

The EVM works with 256-bits. Every operation is based on these base units. If your data is smaller, further operations are needed to downscale from 256 bits to 8 bits. It is only more efficient when you tightly pack variables of type `uint8`, `uint16`, `uint32`, etc into the same storage slot with other adjacent variables smaller than 256 bits.

Here are some instances of this:
File: `DiamondInit.sol` [Line 30](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#L30)
File: `Executor.sol` [Line 367](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L367)

## `++x` is more efficient than `x += 1`

This is the same for subtracting.

For instance:

File: `DiamondCut.sol` [Line 29](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L29)

```
s.diamondCutStorage.currentProposalId += 1;
```

The above can be changed to:

```
++s.diamondCutStorage.currentProposalId;
```

## Unchecking overflow/underflow saves gas.

Checked math, which is the default in ^0.8.0 expends extra gas.

Consider for example:

File: `Diamond.sol` [Line 94-111](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L94-L111)

```
for (uint256 i = 0; i < facetCutsLength; ++i) {
    ...
}
```

Each time `++i` is called, underflow/overflow checks are executed. However, because `i` is already constrained by length, `i < facetCutsLength;`, underflow/overflow checks are unnecessary.

The loop can be rewritten as:

```
for (uint256 i = 0; i < facetCutsLength;) {
    ...

    unchecked {
        ++i;
    }
}
```

Here are some more instances:

File: `Diamond.sol` [Line 132-138](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L132-L138)
File: `Diamond.sol` [Line 153-162](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L153-L162)
File: `Diamond.sol` [Line 173-179](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L173-L179)

## `abi.encodePacked()` is more gas-efficient than `abi.encode()`

The reason `abi.encode()` isn't as gas-efficient is because it pads extra null bytes at the end of the calldata while `abi.encodePacked()` does not.

Here is an example:

File: `DiamondCut.sol` [Line 27](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L27)

```
s.diamondCutStorage.proposedDiamondCutHash = keccak256(abi.encode(_facetCuts, _initAddress));
```

The above could be replaced to:

```
s.diamondCutStorage.proposedDiamondCutHash = keccak256(abi.encodePacked(_facetCuts, _initAddress));
```

Here are a few more instances of this:

File: `DiamondCut.sol` [Line 61](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L61)
File: `Executor.sol` [Line 85](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L85)
File: `Executor.sol` [Line 182](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L182)
