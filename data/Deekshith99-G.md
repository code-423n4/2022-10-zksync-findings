
<h1>Splitting require() statment that uses && Operator  saves gas</h1>

once through this [issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by 3 gas.

Here is the 1 instance of the issue

[Permalink of the code](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/common/AllowList.sol#L96-L101) 


