## No need to recompute ERC20Getters on each deposit

Contract:
[_getERC20Getters](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L164)

Issue:
Name, Symbol and Decimal are always calculated for a token on deposit in L1 even when corresponding L2 token has been deployed. 
This causes wastage of Gas. Ideally this should only be computed for the first time and from next time, collecting & sending this data should be skipped

```
function _getDepositL2Calldata(
        address _l1Sender,
        address _l2Receiver,
        address _l1Token,
        uint256 _amount
    ) internal view returns (bytes memory txCalldata) {
        bytes memory gettersData = _getERC20Getters(_l1Token);

        txCalldata = abi.encodeCall(
            IL2Bridge.finalizeDeposit,
            (_l1Sender, _l2Receiver, _l1Token, _amount, gettersData)
        );
    }

    /// @dev Receives and parses (name, symbol, decimals) from the token contract
    function _getERC20Getters(address _token) internal view returns (bytes memory data) {
        (, bytes memory data1) = _token.staticcall(abi.encodeCall(IERC20Metadata.name, ()));
        (, bytes memory data2) = _token.staticcall(abi.encodeCall(IERC20Metadata.symbol, ()));
        (, bytes memory data3) = _token.staticcall(abi.encodeCall(IERC20Metadata.decimals, ()));
        data = abi.encode(data1, data2, data3);
    }
```

Recommendation:
L2 can send a message to L1 once token at L2 is deployed with L1tokenaddress. Now on L1 side if this L1tokenaddress comes then no need to compute name/decimal/symbol