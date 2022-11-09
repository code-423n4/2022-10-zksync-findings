## Unspecific Pragma Version

The compiler version pragma ^0.8.0 is very unspecific.

Locking the pragma helps ensure that contracts don't get deployed with unintended versions, for example, the latest compiler which may have higher risks of undiscovered bugs.

## Insufficient NatSpec

All the functions and state variables should be well commented using the [NatSpec](https://docs.soliditylang.org/en/develop/natspec-format.html) documentation.

Here are some instances:
File: `Executor.sol` [Line 80-86](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L80-L86)
File: `Executor.sol` [Line 274-293](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L274-L293)
File: `Executor.sol` [Line 372-374](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L372-L374)
File: `Executor.sol` [Line 376-382](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L376-L382)

## Lines are too long

For readability, the maximum suggested line length is 120 characters.

Here are some instances of this issue:

File: `ethereum/contracts/zksync/Config.sol` [Line 8](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L8)
File: `ethereum/contracts/zksync/Config.sol` [Line 59](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L59)

File: `ethereum/contracts/zksync/Storage.sol` [Line 9](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L9)
File: `ethereum/contracts/zksync/Storage.sol` [Line 15](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L15)

File: `ethereum/contracts/zksync/facets/DiamondCut.sol` [Line 104](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L104)
File: `ethereum/contracts/zksync/facets/DiamondCut.sol` [Line 108](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L108)
File: `ethereum/contracts/zksync/facets/DiamondCut.sol` [Line 112](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L112)

File: `ethereum/contracts/zksync/libraries/Diamond.sol` [Line 14](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L14)

## Typo Mistakes

File: [`DiamondProxy.sol`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L29)

```
/// @audit Cotract
7: /// @title Diamond Proxy Cotract (EIP-2535)
```

File: [`Storage.sol`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol)

```
/// @audit addreses
36: /// @dev The sender is an `address` type, although we are using `uint256` for addreses in `L2CanonicalTransaction`.
```

File: [`Plonk4VerifierWithAccessToDNext.sol`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol)

```
@audit callee
613:        // we set first two items in calee side so start idx from 2
```

File: [`libraries/Diamond.sol`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol)

```
@audit dellegate
63:    /// @param initAddress The address that's dellegate called after setting up new facet changes
```
