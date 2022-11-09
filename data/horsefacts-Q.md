## `L1ERC20Bridge`: ERC20 bridge incompatible with positive-rebasing tokens

The `L1ERC20Bridge` stores the user's exact deposit `_amount` (to be used in the event of a failed L2 deposit), and emits this exact amount to trigger an L2 deposit:

[`L1ERC20Bridge#deposit`](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1ERC20Bridge.sol#L111-L146):

```solidity
    function deposit(
        address _l2Receiver,
        address _l1Token,
        uint256 _amount
    ) external payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 txHash) {
        uint256 amount = _depositFunds(msg.sender, IERC20(_l1Token), _amount);
        require(amount > 0, "1T"); // empty deposit amount

        bytes memory l2TxCalldata = _getDepositL2Calldata(msg.sender, _l2Receiver, _l1Token, amount);
        txHash = zkSyncMailbox.requestL2Transaction{value: msg.value}(
            l2Bridge,
            0, // L2 msg.value
            l2TxCalldata,
            DEPOSIT_ERGS_LIMIT,
            new bytes[](0)
        );
[label](https://github.com/code-423n4)
        // Save the deposited amount to claim funds on L1 if the deposit failed on L2
        depositAmount[msg.sender][_l1Token][txHash] = amount;

        emit DepositInitiated(msg.sender, _l2Receiver, _l1Token, amount);
    }

    /// @dev Transfers tokens from the depositor address to the smart contract address
    /// @return The difference between the contract balance before and after the transferring funds
    function _depositFunds(
        address _from,
        IERC20 _token,
        uint256 _amount
    ) internal returns (uint256) {
        uint256 balanceBefore = _token.balanceOf(address(this));
        _token.safeTransferFrom(_from, address(this), _amount);
        uint256 balanceAfter = _token.balanceOf(address(this));

        return balanceAfter - balanceBefore;
    }
```

If a user deposits an interest-bearing or [positive rebasing token](https://github.com/d-xo/weird-erc20#balance-modifications-outside-of-transfers-rebasing--airdrops), like Aave aTokens or Lido stETH, only the amount of their deposit will be bridged to L2. If the locked amount on L1 accrues an additional balance due to interest or rebases, this additional amount cannot be later retrieved by the original depositor.

**Severity:** 
This incompatibility is well known at this point—I considered submitting this as a Medium, but it seems more appropriate to classify as a Low.

**Impact:**
Balances accrued excess of the original deposit will be locked in the bridge contract.

**Recommendation:**
This incompatibility is well known and probably not best solved at the smart contract level. Instead, ensure users are aware of the risk of bridging positive-rebasing tokens, and recommend appropriate wrappers when available (like wstETH for Lido stETH).