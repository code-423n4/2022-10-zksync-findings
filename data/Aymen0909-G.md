# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 2      | Variables inside struct should be packed to save gas  | 1    |
| 2      | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables  |  1 |
| 5      | Splitting `require()` statements that uses && saves gas  |  22 |
| 4      | `++i/i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops |  4  |

## Findings

### 1- Variables inside `struct` should be packed to save gas :

As the solidity EVM works with 32 bytes, variables less than 32 bytes should be packed inside a struct so that they can be stored in the same slot, each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings.

There is 1 instance of this issue:

File: ethereum/contracts/zksync/Storage.sol [Line 17-25](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L17-L25)

```
struct DiamondCutStorage {
    bytes32 proposedDiamondCutHash;
    uint256 proposedDiamondCutTimestamp;
    uint256 lastDiamondFreezeTimestamp;
    uint256 currentProposalId;
    mapping(address => bool) securityCouncilMembers;
    mapping(address => uint256) securityCouncilMemberLastApprovedProposalId;
    uint256 securityCouncilEmergencyApprovals;
}
```

As the variables `proposedDiamondCutTimestam`, `lastDiamondFreezeTimestamp`, `currentProposalId` and `securityCouncilEmergencyApprovals` can't really overflow 2^128, their values should be packed to save gas, the struct should be modified as follow  :

```
struct DiamondCutStorage {
    bytes32 proposedDiamondCutHash;
    mapping(address => bool) securityCouncilMembers;
    mapping(address => uint256) securityCouncilMemberLastApprovedProposalId;
    uint128 proposedDiamondCutTimestamp;
    uint128 lastDiamondFreezeTimestamp;
    uint128 currentProposalId;
    uint128 securityCouncilEmergencyApprovals;
}
```

### 2- `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables :

Using the addition operator instead of plus-equals saves **113 gas** as explained [here](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)

There is 1 instance of this issue:

File: ethereum/contracts/zksync/facets/DiamondCut.sol [Line 29](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L29)
```
s.diamondCutStorage.currentProposalId += 1;
```

### 4- Splitting `require()` statements that uses && saves gas :

There are 2 instances of this issue :

```
File: ethereum/contracts/common/AllowList.sol

96      require(
            callersLength == _targets.length &&
                callersLength == _functionSigs.length &&
                callersLength == _enables.length,
            "yw"
        );

File: ethereum/contracts/common/L2ContractHelper.sol

65      require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf");
```

### 4- `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops :

There are 4 instances of this issue:

```
File: ethereum/contracts/zksync/libraries/Diamond.sol

94      for (uint256 i = 0; i < facetCutsLength; ++i)
132     for (uint256 i = 0; i < selectorsLength; ++i)
153     for (uint256 i = 0; i < selectorsLength; ++i)
173     for (uint256 i = 0; i < selectorsLength; ++i)
```
