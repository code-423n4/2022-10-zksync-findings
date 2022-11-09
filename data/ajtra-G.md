# Index
1. Call to KECCAK256 should use IMMUTABLE rather than constant
2. Require instead of &&
3. != 0 is cheaper than > 0
4. I = I + (-) X is cheaper in gas cost than I += (-=) X
5. Use a more recent version of solidity
6. ABI.ENCODE() is less efficient than ABI.ENCODEPACKED()
7. Use unchecked in for/while loops when it's not possible to overflow	
8. Calldata vs Memory
9. Using storage instead of memory for structs/arrays
10. Move the variable declaration when it's going to be used
11. Remove unnecessary variables
12. Move emit to save local variable


# Details

## 1. Call to KECCAK256 should use IMMUTABLE rather than constant
### Description
Expressions for constant values such as a call to KECCAK256 should use IMMUTABLE rather than constant

### Lines in the code
[L2ContractHelper.sol#L41](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/L2ContractHelper.sol#L41)

## 2. Require instead of &&
### Description
Split of conditions of an require sentence in different requires sentences can save gas

### Lines in the code
[L2ContractHelper.sol#L65](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/L2ContractHelper.sol#L65)
[AllowListed.sol#L96-L101](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/AllowListed.sol#L96-L101)

## 3. != 0 is cheaper than > 0
### Description
Replace all > 0 for != 0

### Lines in the code
[DiamondCut.sol#L16](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/DiamondCut.sol#L16)
[Diamond.sol#L100](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L100)
[Merkle.sol#L23](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Merkle.sol#L23)
[L1ERC20Bridge.sol#L117](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L117)
[L1ERC20Bridge.sol#L210](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L210)

## 4. I = I + (-) X is cheaper in gas cost than I += (-=) X
### Description
In the following example (optimizer = 10000) it's possible to demostrate that I = I + X cost less gas than I += X in state variable.

```solidity
contract Test_Optimization {
    uint256 a = 1;
    function Add () external returns (uint256) {
        a = a + 1;
        return a;
    }
}

contract Test_Without_Optimization {
    uint256 a = 1;
    function Add () external returns (uint256) {
        a += 1;
        return a;
    }
}
```
* Test_Optimization cost is 26324 gas
* Test_Without_Optimization cost is 26440 gas

With this optimization it's possible to **save 116 gas**

### Lines in the code
[DiamondCut.sol#L29](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/DiamondCut.sol#L29)

## 5. Use a more recent version of solidity
### Description
 Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath 
 Use a solidity version of at least 0.8.2 to get compiler automatic inlining 
 Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads 
 Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings 
 Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value. 
 Other improvements in latest release could be found in the following [link](https://github.com/ethereum/solidity/releases)

### Lines in the code
[DiamondProxy.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/DiamondProxy.sol#L3)
[DiamondInit.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/DiamondInit.sol#L3)
[Config.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/Config.sol#L3)
[Storage.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/Storage.sol#L3)
[Base.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Base.sol#L3)
[DiamondCut.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/DiamondCut.sol#L3)
[Executor.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L3)
[Getters.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Getters.sol#L3)
[Governance.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Governance.sol#L3)
[Mailbox.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Mailbox.sol#L3)
[Diamond.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L3)
[Merkle.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Merkle.sol#L3)
[PriorityQueue.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L3)
[IDiamondCut.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L3)
[IExecutor.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IExecutor.sol#L3)
[IGetters.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IGetters.sol#L3)
[IGovernance.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IGovernance.sol#L3)
[IMailbox.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IMailbox.sol#L3)
[IZkSync.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IZkSync.sol#L3)
[L1ERC20Bridge.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L3)
[L1EthBridge.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L3)
[IL1Bridge.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L3)
[IL2Bridge.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/interfaces/IL2Bridge.sol#L3)
[AllowList.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/AllowList.sol#L3)
[IAllowList.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/interfaces/IAllowList.sol#L3)
[AllowListed.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/AllowListed.sol#L3)
[L2ContractHelper.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/L2ContractHelper.sol#L3)
[ReentrancyGuard.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/ReentrancyGuard.sol#L3)
[UncheckedMath.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/libraries/UncheckedMath.sol#L3)
[UnsafeBytes.sol#L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/libraries/UnsafeBytes.sol#L3)

## 6. ABI.ENCODE() is less efficient than ABI.ENCODEPACKED()
### Description
**abi.encode** will apply [ABI encoding rules](https://docs.soliditylang.org/en/v0.8.17/abi-spec.html). Therefore all elementary types are padded to 32 bytes and dynamic arrays include their length. 
Therefore it is possible to also decode this data again (with abi.decode) when the type are known.

**abi.encodePacked** will only use the only use the minimal required memory to encode the data. E.g. an address will only use 20 bytes and for dynamic arrays only the elements will be stored without length. 
For more info see the [Solidity docs for packed mode](https://docs.soliditylang.org/en/v0.8.17/abi-spec.html?highlight=encodepacked#non-standard-packed-mode)

For the input of the keccak method it is important that you can ensure that the resulting bytes of the encoding are unique. So if you always encode the same types and arrays 
always have the same length then there is no problem. But if you switch the parameters that you encode or encode multiple dynamic arrays you might have conflicts.

For example:

abi.encodePacked(address(0x0000000000000000000000000000000000000001), uint(0)) encodes to the same as abi.encodePacked(uint(0x0000000000000000000000000000000000000001000000000000000000000000), address(0))

and

abi.encodePacked(uint[](1,2), uint[](3)) encodes to the same as abi.encodePacked(uint[](1), uint[](2,3))

Therefore these examples will also generate the same hashes even so they are different inputs.

On the other hand you require less memory and therefore in most cases abi.encodePacked uses less gas than abi.encode.


### Lines in the code
[DiamondInit.sol#L54](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/DiamondInit.sol#L54)
[DiamondCut.sol#L27](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/DiamondCut.sol#L27)
[DiamondCut.sol#L61](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/DiamondCut.sol#L61)
[Executor.sol#L85](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L85)
[Executor.sol#L122](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L122)
[Executor.sol#L182](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L182)
[Executor.sol#L359](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L359)
[Executor.sol#L381](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L381)
[Executor.sol#L386](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L386)
[Mailbox.sol#L163](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Mailbox.sol#L163)
[Merkle.sol#L30](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Merkle.sol#L30)
[Merkle.sol#L32](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Merkle.sol#L32)
[L1ERC20Bridge.sol#L82](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L82)
[L1ERC20Bridge.sol#L168](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L168)
[L1ERC20Bridge.sol#L283](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L283)
[L1EthBridge.sol#L58](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L58)
	
	
## 7. Use unchecked in for/while loops when it's not possible to overflow	
### Description
Use unchecked { i++; } or unchecked{ ++i; } when it's not possible to overflow to save gas.
	
### Lines in the code
[Diamond.sol#L94](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L94)
[Diamond.sol#L132](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L132)
[Diamond.sol#L153](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L153)
[Diamond.sol#L173](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L173)

## 8. Calldata vs Memory
### Description
Use calldata instead of memory in a function parameter when you are only to read the data can save gas by storing it in calldata

### Lines in the code
[Executor.sol#L23](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L23)
[Executor.sol#L80](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L80)
[Executor.sol#L152](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L152)
[Executor.sol#L190](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L190)
[Executor.sol#L385](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L385)
[Diamond.sol#L89](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L89)
[Diamond.sol#L167](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L167)
[PriorityQueue.sol#L54](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L54)

## 9. Using storage instead of memory for structs/arrays
### Description
When retrieving data from a memory location, assigning the data to a memory variable causes all fields of the struct/array to be read from memory, resulting in a Gcoldsload (2100 gas) 
for each field of the struct/array. When reading fields from new memory variables, they cause an extra MLOAD instead of a cheap stack read. Rather than declaring a variable with the memory 
keyword, it is much cheaper to declare a variable with the storage keyword and cache all fields that need to be read again in a stack variable, because the fields actually read 
will only result in a Gcoldsload. The only case where the entire struct/array is read into a memory variable is when the entire struct/array is returned by a function, 
passed to a function that needs memory, or when the array/struct is read from another store array/struc

### Lines in the code
[DiamondProxy.sol#L26](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/DiamondProxy.sol#L26)
[Getters.sol#L165](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Getters.sol#L165)
[Diamond.sol#L98](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L98)
[Diamond.sol#L134](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L134)
[Diamond.sol#L155](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L155)
[Diamond.sol#L175](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L175)

## 10. Move the variable declaration when it's going to be used
### Description
There are situations in the code where there are require or if-revert that can be executed without the declaration of some variables. That variables can be
declared after those requires and save gas in some situations.

#### DiamondCut.executeDiamondCutProposal.diamondStorage

```diff
-  Diamond.DiamondStorage storage diamondStorage = Diamond.getDiamondStorage();

   bool approvedBySecurityCouncil = s.diamondCutStorage.securityCouncilEmergencyApprovals >=
       SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE;

   bool upgradeNoticePeriodPassed = block.timestamp >=
       s.diamondCutStorage.proposedDiamondCutTimestamp + UPGRADE_NOTICE_PERIOD;

   require(approvedBySecurityCouncil || upgradeNoticePeriodPassed, "a6"); // notice period should expire
+  Diamond.DiamondStorage storage diamondStorage = Diamond.getDiamondStorage();
   require(approvedBySecurityCouncil || !diamondStorage.isFrozen, "f3");
```
[DiamondCut.sol#L47-L56](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/DiamondCut.sol#L47-L56)

## 11. Remove unnecessary variables


```diff
-	bool success = zkSyncMailbox.proveL2LogInclusion(_l2BlockNumber, _l2MessageIndex, l2Log, _merkleProof);
-   require(success, "ju");
+   require(zkSyncMailbox.proveL2LogInclusion(_l2BlockNumber, _l2MessageIndex, l2Log, _merkleProof), "ju");
```
[L1EthBridge.sol#L165-L166](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L165-L166)

```diff
-	uint256 balanceAfter = _token.balanceOf(address(this));

-   return balanceAfter - balanceBefore;
+   return _token.balanceOf(address(this)) - balanceBefore;
```
[L1ERC20Bridge.sol#L143-L145](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L143-L145)

```diff
-	bool success = zkSyncMailbox.proveL2LogInclusion(_l2BlockNumber, _l2MessageIndex, l2Log, _merkleProof);
-	require(success, "yn");
+	require(zkSyncMailbox.proveL2LogInclusion(_l2BlockNumber, _l2MessageIndex, l2Log, _merkleProof), "yn");
```
[L1ERC20Bridge.sol#L206-L207](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L206-L207)

```diff
-	bool successVerifyProof = s.verifier.verify_serialized_proof(proofPublicInput, _proof.serializedProof);
-	require(successVerifyProof, "p"); // Proof verification fail
+	require(s.verifier.verify_serialized_proof(proofPublicInput, _proof.serializedProof), "p"); // Proof verification fail
	
	// Verify the recursive part that was given to us through the public input
-	bool successProofAggregation = _verifyRecursivePartOfProof(_proof.recurisiveAggregationInput);
-	require(successProofAggregation, "hh"); // Proof aggregation must be valid
+	require(_verifyRecursivePartOfProof(_proof.recurisiveAggregationInput), "hh"); // Proof aggregation must be valid
```
[Executor.sol#L260-L265](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L260-L265)


## 12. Move emit to save local variable

```diff
    function setPendingOwner(address _newPendingOwner) external onlyOwner {
        // Save previous value into the stack to put it into the event later
-       address oldPendingOwner = pendingOwner;

-       if (oldPendingOwner != _newPendingOwner) {
+       if (pendingOwner != _newPendingOwner) {
+           emit NewPendingOwner(pendingOwner, _newPendingOwner);
            // Change pending owner
            pendingOwner = _newPendingOwner;

-           emit NewPendingOwner(oldPendingOwner, _newPendingOwner);
        }
    }
```
[AllowList.sol#L140-L150](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/AllowList.sol#L140-L150)

