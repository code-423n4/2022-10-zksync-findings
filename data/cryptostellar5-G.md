### G-01 SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

*Number of Instances Identified: 2*


https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L96-L101

```
require(
            callersLength == _targets.length &&
                callersLength == _functionSigs.length &&
                callersLength == _enables.length,
            "yw"
        );
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L65

```
require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf");
```

### G-02 X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES, SIMILARLY X/=Y COSTS MORE GAS THAN X = X / Y.

*Number of Instances Identified: 2*

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol

```
29: s.diamondCutStorage.currentProposalId += 1;
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol

```
34:  _index /= 2;
```


### G-03 MULTIPLICATION or DIVISION BY TWO SHOULD USE BIT SHIFTING

*Number of Instances Identified: 1*

`<x> * 2` is equivalent to `<x> << 1` and `<x> / 2` is the same as `<x> >> 1`. The `MUL` and `DIV` opcodes cost 5 gas, whereas `SHL` and `SHR` only cost 3 gas

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol

```
34:  _index /= 2;
```


### G-04 INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

*Number of Instances Identified: 7*

Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol

```
136: function _depositFunds
149: function _getDepositL2Calldata
164: function _getERC20Getters
260: function _parseL2WithdrawalMessage
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol

```
113: function _getDepositL2Calldata
214: function _parseL2WithdrawalMessage
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol

```
71: function bytecodeLen
```


### G-05 IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED

*Number of Instances Identified: 1*

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol

```
33: address constant CONVENTIONAL_ETH_ADDRESS = address(0);
```

### G-06 USAGE OF UINTS or INTS SMALLER THAN 32 BYTES - 256 BITS INCURS OVERHEAD

*Number of Instances Identified: 9*

> When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol

```
185: uint16 _l2TxNumberInBlock
228: uint16 _l2TxNumberInBlock
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol

```
138: uint16 _l2TxNumberInBlock
185: uint16 _l2TxNumberInBlock
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/interfaces/IL1Bridge.sol

```
29: uint16 _l2TxNumberInBlock
36: uint16 _l2TxNumberInBlock
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol

```
42: uint16 txNumberInBlock;
54: uint16 txNumberInBlock
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol

```
29: uint8 private decimals_
``` 

### G-07 ++I or I++ SHOULD BE UNCHECKED{++I} or UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

*Number of Instances Identified: 4*

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol

```
94: for (uint256 i = 0; i < facetCutsLength; ++i) {
132: for (uint256 i = 0; i < selectorsLength; ++i) {
153: for (uint256 i = 0; i < selectorsLength; ++i) {
173: for (uint256 i = 0; i < selectorsLength; ++i) {
```

### G-08 USING BOTH NAMED RETURNS AND A RETURN STATEMENT ISNT NECESSARY

*Number of Instances Identified: 1*

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol

```
26: returns (StoredBlockInfo memory storedNewBlock)
```


### G-09 USING REQUIRE() INSTEAD OF ASSERT() HELPS SAVE GAS

*Number of Instances Identified: 1*

Between solc 0.4.10 and 0.8.0, `require()` used `REVERT` (0xfd) opcode which refunded remaining gas on failure while `assert()` used `INVALID` (0xfe) opcode which consumed all the supplied gas. (see [https://docs.soliditylang.org/en/v0.8.1/control-structures.html#error-handling-assert-require-revert-and-exceptions](https://docs.soliditylang.org/en/v0.8.1/control-structures.html#error-handling-assert-require-revert-and-exceptions)).  
`require()` should be used for checking error conditions on inputs and return values while `assert()` should be used for invariant checking (**properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix**).  
From the current usage of `assert`, my guess is that they can be replaced with `require`, unless a `Panic` really is intended.

Here are the `assert` locations:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol

```
16: assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);
```

