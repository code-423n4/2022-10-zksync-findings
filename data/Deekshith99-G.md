
## [G-1]Splitting require() statment that uses && Operator  saves gas

once through this [issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by 3 gas.

Here is the 1 instance of the issue

[Permalink of the code](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/common/AllowList.sol#L96-L101) 


## [G-2} USE A MORE RECENT VERSION OF SOLIDITY
 
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining , Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

