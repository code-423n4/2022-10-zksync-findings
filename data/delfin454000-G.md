### Avoid use of '&&' within a `require` function
Splitting into separate `require()` statements saves gas
___
[AllowList.sol: L96-101](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/common/AllowList.sol#L96-L101)
```solidity
        require(
            callersLength == _targets.length &&
                callersLength == _functionSigs.length &&
                callersLength == _enables.length,
            "yw"
        ); // The size of arrays should be equal
```
Recommendation:
```solidity
        require(callersLength == _targets.length, "yw");
        require(callersLength == _functionSigs.length, "yw");
        require(callersLength == _enables.length,"yw");
        // The size of arrays should be equal
```
___
[L2ContractHelper.sol: L65](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/common/L2ContractHelper.sol#L65)
```solidity
        require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
```
Recommendation:
```solidity
        require(version == 1, "zf");
        require(_bytecodeHash[1] == bytes1(0),"zf");
        // Incorrectly formatted bytecodeHash
```
___
___

### Avoid use of default "checked" behavior in a `for` loop
Underflow/overflow checks are made every time `++i` (or equivalent counter) is called. Such checks are unnecessary since `i` is already limited. Therefore, use `unchecked {++i}` instead
___
[Diamond.sol: L94](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L94)
```solidity
        for (uint256 i = 0; i < facetCutsLength; ++i) {
```
___
[Diamond.sol: L132](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L132)
```solidity
        for (uint256 i = 0; i < selectorsLength; ++i) {
```
___
[Diamond.sol: L153](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L153)
```solidity
        for (uint256 i = 0; i < selectorsLength; ++i) {
```
___
[Diamond.sol: L173](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L173)
```solidity
        for (uint256 i = 0; i < selectorsLength; ++i) {
```
___
___
