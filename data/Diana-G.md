## G-01 x += y COSTS MORE GAS THAN x = x + y FOR STATE VARIABLES (SAME APPLIES FOR x -= y , x = x - y)

_There are 2 instances of this issue_

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol

```
File: contracts/zksync/facets/DiamondCut.sol

29: s.diamondCutStorage.currentProposalId += 1;
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol

```
File: contracts/zksync/libraries/Merkle.sol

34: _index /= 2;
```

--------------------

## G-02 INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

Not inlining costs 20 to 40 gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.

_There are 10 instances of this issue_

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol

```
File: contracts/bridge/L1ERC20Bridge.sol

136: function _depositFunds(
149: function _getDepositL2Calldata(
164: function _getERC20Getters(address _token) internal view returns (bytes memory data) {
260: function _parseL2WithdrawalMessage(bytes memory _l2ToL1message)
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol

```
File: contracts/bridge/L1EthBridge.sol

113: function _getDepositL2Calldata(
214: function _parseL2WithdrawalMessage(bytes memory _message)
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol

```
File: contracts/common/L2ContractHelper.sol

71: function bytecodeLen(bytes32 _bytecodeHash) internal pure returns (uint256 codeLengthInWords) {
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol

```
File: contracts/zksync/facets/Executor.sol

23: function _commitOneBlock(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)
89: function _processL2Logs(CommitBlockInfo calldata _newBlock)
177: function _collectOperationsFromPriorityQueue(uint256 _nPriorityOps) internal returns (bytes32 concatHash) {
```

---------------

## G-03 Multiplication or division by 2 should use bit shifting

`<x> * 2` is equivalent to `<x> << 1` and `<x> / 2` is the same as `<x> >> 1`. The `MUL` and `DIV` opcodes cost 5 gas, whereas `SHL` and `SHR` only cost 3 gas

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol

```
File: contracts/zksync/libraries/Merkle.sol

34: _index /= 2;
```

--------------

## G-04 SPLITTING REQURE() STATEMENTS THAT USE && SAVES GAS

_There are 2 instances of this issue_

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol

```
File: contracts/common/AllowList.sol

96-101: require(
            callersLength == _targets.length &&//@audit-info Splitting require() statements that use && saves gas
                callersLength == _functionSigs.length &&
                callersLength == _enables.length,
            "yw"
        );
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol

```
File: contracts/common/L2ContractHelper.sol

65: require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf");
```

-------------

## G-05 USE REQUIRE() INSTEAD OF ASSERT()

Between solc 0.4.10 and 0.8.0, `require()` used `REVERT` (0xfd) opcode which refunded remaining gas on failure while `assert()` used `INVALID` (0xfe) opcode which consumed all the supplied gas..

`require()` should be used for checking error conditions on inputs and return values while `assert()` should be used for invariant checking (**properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix**).  
From the current usage of `assert`, my guess is that they can be replaced with `require`, unless a `Panic` really is intended.

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol

```
File: contracts/zksync/facets/DiamondCut.sol

16: assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);
```

----------------

## G-06 INCREMENTS CAN BE UNCHECKED

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline

Prior to Solidity 0.8.0, arithmetic operations would always wrap in case of under- or overflow leading to widespread use of libraries that introduce additional checks.

Since Solidity 0.8.0, all arithmetic operations revert on over- and underflow by default, thus making the use of these libraries unnecessary.

To obtain the previous behaviour, an unchecked block can be used

_There are 4 instances of this issue_

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol

```
File: contracts/zksync/libraries/Diamond.sol

94: for (uint256 i = 0; i < facetCutsLength; ++i) {
132: for (uint256 i = 0; i < selectorsLength; ++i) {
153: for (uint256 i = 0; i < selectorsLength; ++i) {
173: for (uint256 i = 0; i < selectorsLength; ++i) {
```

----------------

## G-07 IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol

```
File: contracts/bridge/L1EthBridge.sol

33: address constant CONVENTIONAL_ETH_ADDRESS = address(0);
```

---------------------------

## G-08 USAGE OF UINTS or INTS SMALLER THAN 32 BYTES - 256 BITS INCURS OVERHEAD

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

_There are 9 instances of this issue_

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol

```
File: contracts/bridge/L1ERC20Bridge.sol

185: uint16 _l2TxNumberInBlock,
228: uint16 _l2TxNumberInBlock,
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol

```
File: contracts/bridge/L1EthBridge.sol

138: uint16 _l2TxNumberInBlock
185: uint16 _l2TxNumberInBlock
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/interfaces/IL1Bridge.sol

```
File: contracts/bridge/interfaces/IL1Bridge.sol

29: uint16 _l2TxNumberInBlock
36: uint16 _l2TxNumberInBlock
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol

```
File: contracts/common/L2ContractHelper.sol

42: uint16 txNumberInBlock;
54: uint16 txNumberInBlock
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol

```
File: contracts/bridge/L1ERC20Bridge.sol

29: uint8 private decimals_
```

-------------

## G-09 USING BOTH NAMED RETURNS AND A RETURN STATEMENT ISN'T NECESSARY

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol

```
File: contracts/zksync/facets/Executor.sol

26: returns (StoredBlockInfo memory storedNewBlock)
```

-------------
## G-10 INTERNAL FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE REMOVED TO SAVE DEPLOYMENT GAS 

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol

```
File: contracts/zksync/facets/Executor.sol

80: function _calculateBlockHash(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)
```

-------------

## G-11 EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

_There are 2 instances of this issue_

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol

```
File: contracts/zksync/Config.sol

5: bytes32 constant EMPTY_STRING_KECCAK = 0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470;
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol

```
File: contracts/common/L2ContractHelper.sol

24: bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
```

---------

