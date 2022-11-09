#### [G-01] Revert transactions for debugging and returning gas on failed txs
#### [G-02] Use `bytes.concat` instead of `abi.encodePacked`
---
#### [G-01] Revert transactions for debugging and returning gas on failed txs
ethereum/contracts/common/AllowList.sol
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L144
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L157

Recommend adding `else` branch with error messages    
```sol
else {
	revert sameOwner(); 
}
```

```sol
else {
	revert samePendingOwner();
}
```

#### [G-02]  Use `bytes.concat` instead of `abi.encodePacked`
zksync/contracts/bridge/L2ERC20Bridge.sol
- https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L109

Recommend using version ^0.8.12 and changing to `bytes.concat`