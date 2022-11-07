
## G.1 Retrieve data from calldata, rather than relying purely on logs will save gas 
zySync relies on event logs to trigger txs. The info in logs are quire comprehensive, however, there are cases where the same info can be obtained through function calldata. this will save quite some gas
true!

---

## G.2 use of abi.encodePacked will use less gas than abi.encode
There are multiple places where abi.encodePacked should be used in place of abi.encode. Given the value types, they will result in the same bytes, yet with less gas. As example:
[`keccak256(abi.encode(passThroughDataHash, metadataHash, auxiliaryOutputHash));`](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L359)
[`return abi.encode(_block.l2LogsTreeRoot, l2ToL1LogsHash, initialStorageChangesHash, repeatedStorageChangesHash);`](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L381)