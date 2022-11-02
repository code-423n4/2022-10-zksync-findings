# Use Shift Right/Left instead of Division/Multiplication for Gas Optimizations
____________________
## Description

A division/multiplication by any number `x` being a power of 2 can be calculated by shifting `log2(x)` to the right/left.

While the `DIV` opcode uses 5 gas, the `SHR` opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.
____________________

## Example

Do not use:
```
uint256 b = a / 2;
uint256 c = a / 4;
uint256 d = a * 8;
```
Use the following instead:
```
uint256 b = a >> 1;
uint256 c = a >> 2;
uint256 d = a << 3;
```
____________________
## Proofs of the issue
```
ethereum/contracts/common/L2ContractHelper.sol::26
uint160 constant SYSTEM_CONTRACTS_OFFSET = 0x8000; // 2^15

ethereum/contracts/common/L2ContractHelper.sol::72
codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));

ethereum/contracts/zksync/Config.sol::23
uint256 constant INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + 64 * 4896;

zksync/contracts/L2ContractHelper.sol::17
uint160 constant SYSTEM_CONTRACTS_OFFSET = 0x8000; // 2^15
```
____________________
### Background Information
- https://www.evm.codes