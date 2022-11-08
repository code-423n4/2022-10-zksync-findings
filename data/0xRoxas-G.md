# Gas Report
## Optimizations found [5]

### [G-01] Use Bit Shift Operators for MUL/DIV/MOD of Powers of 2 [ⓘ](https://github.com/devanshbatham/Solidity-Gas-Optimization-Tips#1--use-of-bit-shift-operators)

#### Findings [6]:

*/ethereum/contracts/common/L2ContractHelper.sol*
Line(s): [50](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L50), [52](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L52)
```solidity
50:	require(_bytecode.length % 32 == 0, "po");
52:	uint256 bytecodeLenInWords = _bytecode.length / 32;
```
**suggested change**
```solidity
50:	require(_bytecode.length & 31 == 0, "po");
52:	uint256 bytecodeLenInWords = _bytecode.length >> 5;
```
*/ethereum/contracts/zksync/Config.sol*
Line(s): [12](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L12)
```solidity
12:	uint256 constant L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;
```
**suggested change**
```solidity
12:	uint256 constant L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE << 9;
```

*/ethereum/contracts/zksync/libraries/Merkle.sol*
Line(s): [29](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol#L29), [34](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol#L34)
```solidity
29:	if (_index % 2 == 0) {
34:	_index /= 2;
```

**suggested changes**
```solidity
29:	if (_index & 1 == 0) {
34:	_index = _index >> 1;
```

*/ethereum/contracts/common/L2ContractHelper.sol*
Line(s): [54](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L54), [67](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L67), [72](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L72)
```solidity
54:	require(bytecodeLenInWords % 2 == 1, "pr");
67:	require(bytecodeLen(_bytecodeHash) % 2 == 1, "uy");
72:	codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
```

**suggested changes**
```solidity
54:	require(bytecodeLenInWords & 1 == 1, "pr");
67:	require(bytecodeLen(_bytecodeHash) & 1 == 1, "uy");
72:	codeLengthInWords = uint256(uint8(_bytecodeHash[2])) << 8 + uint256(uint8(_bytecodeHash[3]));
```

### [G-02] Unless Used for Variable Packing uint8 May Be More Expensive Than Using uint256 [ⓘ](https://yos.io/2021/05/17/gas-efficient-solidity/#tip-15-usage-of-uint8-may-increase-gas-cost)

#### Findings [2]:

*/ethereum/contracts/common/L2ContractHelper.sol*
Line(s): [64](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L64)
```solidity
64:	uint8 version = uint8(_bytecodeHash[0]);
```

*/zksync/contracts/bridge/L2StandardERC20.sol*
Line(s): [29](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L29)
```solidity
29:	uint8 private decimals_;
```

### [G-03] ++i is cheaper than i += 1 [ⓘ](https://yos.io/2021/05/17/gas-efficient-solidity/#tip-12-i-costs-less-gas-compared-to-i-or-i--1)

#### Findings [1]:

*/ethereum/contracts/zksync/facets/DiamondCut.sol*
Line(s): [29](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L29)
```solidity
29:	s.diamondCutStorage.currentProposalId += 1;
```
**Suggested Change**
```solidity
29:	++s.diamondCutStorage.currentProposalId;
```

### [G-04] Public `constant` Can Be made `private` to Save Gas

*/ethereum/contracts/zksync/libraries/Diamond.sol*
```solidity
14:	bytes32 constant DIAMOND_STORAGE_POSITION = 0xc8fcad8db84d3cc18b4c41d551ea0ee66dd599cde068d998e57d5e09332c131b;
```

### [G-05] Break Apart Require Statments Instead of && [ⓘ](https://yos.io/2021/05/17/gas-efficient-solidity/#tip-11-splitting-require-statements-that-use--saves-gas)

Breaking apart require and if statements saves gas.

#### Findings [1]:

*/ethereum/contracts/common/AllowList.sol*
Line(s): [97](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L97)
```solidity
96:	require(
97:		callersLength == _targets.length &&
98:			callersLength == _functionSigs.length &&
99:			callersLength == _enables.length,
100:		"yw"
101:	); // The size of arrays should be equal
```