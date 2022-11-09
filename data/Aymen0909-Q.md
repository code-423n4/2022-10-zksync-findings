# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1      | Immutable state variables lack zero address checks | Low | 4 |
| 2      | `require()`/`revert()` statements should have descriptive reason strings | NC | 7 |
| 3      | `2**<N> - 1` should be re-written as `type(uint<N>).max`  | NC | 1 |


## Findings

### 1- Immutable state variables lack zero address checks  :

Constructors should check the values written in an immutable state variables(address, uint, int) is not the zero value (address(0) or 0)

#### Impact - Low Risk

#### Proof of Concept
Instances include:

File: ethereum/contracts/bridge/L1ERC20Bridge.sol [Line 58](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L58)
```
zkSyncMailbox = _mailbox;
```

File: ethereum/contracts/bridge/L1ERC20Bridge.sol [Line 59](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L59)
```
allowList = _allowList;
```

File: ethereum/contracts/bridge/L1EthBridge.sol [Line 49](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L49)
```
zkSyncMailbox = _mailbox;
```

File: ethereum/contracts/bridge/L1EthBridge.sol [Line 50](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L50)
```
allowList = _allowList;
```

#### Mitigation
Add non-zero address checks in the constructors for the instances aforementioned.

### 2- `require()`/`revert()` statements should have descriptive reason strings :

#### Risk : NON CRITICAL

#### Proof of Concept
Instances include:
 
```
File: contracts/bridge/L1EthBridge.sol

145     require(amount != 0);
221     require(_message.length == 56);

File: ethereum/contracts/zksync/facets/Executor.sol

43      require(expectedNumberOfLayer1Txs == _newBlock.numberOfLayer1Txs);
45      require(l2BlockTimestamp == _newBlock.timestamp);
297     require(_recurisiveAggregationInput.length == 4);

File: zksync/contracts/bridge/L2ETHBridge.sol

50      require(_l1Token == CONVENTIONAL_ETH_ADDRESS);

File: zksync/contracts/bridge/L2StandardERC20.sol

96      require(msg.sender == l2Bridge);
```

#### Mitigation
Add reason strings to the aforementioned require statements for better comprehension.

### 3- `2**<N> - 1` should be re-written as `type(uint<N>).max` :

#### Impact - NON CRITICAL

#### Proof of Concept
Instances include:

File: ethereum/contracts/common/L2ContractHelper.sol

[require(bytecodeLenInWords < 2**16, "pp");](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L53)

#### Mitigation
Replace the aforementioned statements for better readability.