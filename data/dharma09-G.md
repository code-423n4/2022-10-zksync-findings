## 1. REQUIRE STATEMENTS INCLUDING CONDITIONS WITH THE `&&` OPERATOR CAN BE BROKEN DOWN IN MULTIPLE REQUIRE STATEMENTS TO SAVE GAS.

## PROOF OF CONCEPT
If you’re using the Optimizer at 200, instead of using the `&&` operator in a single require statement to check multiple conditions, Consider using multiple require statements with 1 condition per require statement:


Instances include:
[AllowList.sol#L96](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L96)
```
 require(callersLength == _targets.length ,"yw");
 require(callersLength == _functionSigs.length,"yw");
 require(callersLength == _enables.length,"yw");
```
[L2ContractHelper.sol#L65](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L65)
```
require(version == 1, "zf") 
require(_bytecodeHash[1] == bytes1(0), "zf");
```