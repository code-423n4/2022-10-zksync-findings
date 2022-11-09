1. Need to double check "balanceAfter - balanceBefore == _amount" to make sure token transfers successfully and correctly.

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L145

return balanceAfter - balanceBefore;

It is suggested to add the following codes before this line of codes to make sure token transfers successfully and correctly.

require(balanceAfter - balanceBefore != _amount);