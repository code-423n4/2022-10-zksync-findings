# Use != 0 instead of > 0 for Unsigned Integer Comparison
__________
## Description

When dealing with unsigned integer types, comparisons with != 0 are cheaper than with > 0.
__________
## Example

Do not use:
```
// `a` being of type unsigned integer
require(a > 0, "!a > 0");
```

Use the following:
```
// `a` being of type unsigned integer
require(a != 0, "!a > 0");
```
__________
## Issue locations

```
ethereum/contracts/bridge/L1ERC20Bridge.sol::117
require(amount > 0, "1T");

ethereum/contracts/bridge/L1ERC20Bridge.sol::210
require(amount > 0, "y1");

ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol::304
equire(vk.num_inputs > 0);

ethereum/contracts/zksync/facets/DiamondCut.sol::16
assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);

ethereum/contracts/zksync/libraries/Diamond.sol::100
require(selectors.length > 0, "B");

ethereum/contracts/zksync/libraries/Merkle.sol::23
require(pathLength > 0, "xc");
```