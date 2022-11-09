# Gas Optimizations

**Optimizer settings:**

```
optimizer: {
                enabled: true,
                runs: 200
            }
```

## [G-01] Use unchecked increment in `DiamonCut.sol`

| Methods                   | $\Delta$ Average gas |
| :------------------------ | :------------------- |
| executeDiamondCutProposal | 1 227                |
| diamondCut                | 549                  |

| Deployment      | $\Delta$ Average gas |
| :-------------- | :------------------- |
| DiamondCutFacet | 12 518               |
| DiamondCutTest  | 14 257               |
| DiamondProxy    | 17 123               |

For instance, change this:

```solidity
        for (uint256 i = 0; i < facetCutsLength; ++i) {
            ...
            }
```

To this:

```solidity
        for (uint256 i = 0; i < facetCutsLength; ) {
            ...
            unchecked{
                ++i;
            }
```

[Diamond.sol#L94](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L94)

```solidity
        for (uint256 i = 0; i < facetCutsLength; ++i) {
```

[Diamond.sol#L132](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L132)

```solidity
        for (uint256 i = 0; i < selectorsLength; ++i) {
```

[Diamond.sol#L153](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L153)

```solidity
        for (uint256 i = 0; i < selectorsLength; ++i) {
```

[Diamond.sol#L173](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L173)

```solidity
        for (uint256 i = 0; i < selectorsLength; ++i) {
```