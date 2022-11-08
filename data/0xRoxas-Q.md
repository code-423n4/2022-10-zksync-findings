# QA Report
## Found NC [2]

### [NC-01] Require Statements Missing Error Message

Error messages help with troubleshooting, some require statements are missing error messages.

#### Findings:

*/ethereum/contracts/zksync/facets/Executor.sol*
Line(s): [43](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L43), [45](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L45), [297](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L297)
```solidity
43:	require(expectedNumberOfLayer1Txs == _newBlock.numberOfLayer1Txs);
45:	require(l2BlockTimestamp == _newBlock.timestamp);
297:	require(_recurisiveAggregationInput.length == 4);
```

*/ethereum/contracts/bridge/L1EthBridge.sol*
Line(s): [145](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L145), [221](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L221), [224](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L224), [238](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L238)
```solidity
145:	require(amount != 0);
221:	require(_message.length == 56);
224:	require(bytes4(functionSignature) == this.finalizeWithdrawal.selector);
238:	require(callSuccess);
```

*/zksync/contracts/bridge/L2ETHBridge.sol*
Line(s): [50](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L50)
```solidity
50:	require(_l1Token == CONVENTIONAL_ETH_ADDRESS);
```


### [NC-02] Underscore Notation Improves Readability

Solidity allows for the use of _ between every 3 digits of large numbers.

#### Findings:

*/ethereum/contracts/zksync/Config.sol*
Line(s): [23](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L23), [26](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L26), [77](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L77)
```solidity
23:	uint256 constant INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + 64 * 4896;
26:	uint256 constant REPEATED_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + 40 * 7787;
77:	uint256 constant PRIORITY_TX_MAX_ERGS_LIMIT = 2097152;
```

*/ethereum/contracts/bridge/L1ERC20Bridge.sol*
Line(s): [32](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L32), [36](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L36)
```solidity
32:	uint256 constant DEPOSIT_ERGS_LIMIT = 2097152;
36:	uint256 constant DEPLOY_L2_BRIDGE_COUNTERPART_ERGS_LIMIT = 2097152;
```

*/ethereum/contracts/bridge/L1EthBridge.sol*
Line(s): [26](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L26), [30](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L30)
```solidity
26:	uint256 constant DEPOSIT_ERGS_LIMIT = 2097152;
30:	uint256 constant DEPLOY_L2_BRIDGE_COUNTERPART_ERGS_LIMIT = 2097152;
```