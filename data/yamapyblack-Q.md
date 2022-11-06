## Title

Should `deposit` in `L1EthBridge.sol` revert amount zero?

### Links to affected code

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L88-L96

### Details

Anyone can `deposit` in `L1EthBridge.sol` with amount 0. If the amount is 0, all sent ETH are included in `zkSyncFee`.

```
uint256 zkSyncFee = msg.value - _amount;
```

However, `deposit` in `L1ERC20Bridge.sol` require amount is over 0. 

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L117

```
require(amount > 0, "1T"); // empty deposit amount
```

Also if deposited with amount 0 and the storage of depositAmount has amount 0, `claimFailedDeposit` in `L1EthBridge.sol` causes an error.

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L145

```
require(amount != 0);
```

Should `deposit` in `L1EthBridge.sol` have the validation of amount zero?
