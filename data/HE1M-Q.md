#### NO. 1
The critical parameters in `initialize(...)` are not set safely:

 - `s.governor` should be set to `msg.sender`, because a wrong governor address will result in loss of access to all other parts, and later changing the governor to the correct address.
 - `_l2BootloaderBytecodeHash` should be validated like `L2ContractHelper.validateBytecodeHash(_l2BootloaderBytecodeHash)` as in `GovernanceFacet`
 - `_l2DefaultAccountBytecodeHash` should be validated like `L2ContractHelper.validateBytecodeHash(_l2DefaultAccountBytecodeHash)` as in `GovernanceFacet`

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/DiamondInit.sol#L39
https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/DiamondInit.sol#L58
https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/DiamondInit.sol#L59

#### NO. 2
`decimals()`, `name()`, and `symbol()` are not part of the official ERC20 standard and might fail for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.

```
function _getERC20Getters(address _token) internal view returns (bytes memory data) {
        (, bytes memory data1) = _token.staticcall(abi.encodeCall(IERC20Metadata.name, ()));
        (, bytes memory data2) = _token.staticcall(abi.encodeCall(IERC20Metadata.symbol, ()));
        (, bytes memory data3) = _token.staticcall(abi.encodeCall(IERC20Metadata.decimals, ()));
        data = abi.encode(data1, data2, data3);
    }
```

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1ERC20Bridge.sol#L164