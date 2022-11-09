# non-crit

## NC-1 emit event after cancellation was successful
The code reads better that way.

```javascript
File: ethereum/contracts/zksync/facets/DiamondCut.sol

35:    function cancelDiamondCutProposal() external onlyGovernor {
36:        emit DiamondCutProposalCancelation(
37:            s.diamondCutStorage.currentProposalId,
38:            s.diamondCutStorage.proposedDiamondCutHash
39:        );
40:        require(_resetProposal(), "g1"); // failed cancel diamond cut
41:    }
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L36-L39

## NC-2 long row

GitHub starts using a scroll bar when the length is over 164 characters, the lines below should be split when they reach that length.

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L104
```javascript
File: ethereum/contracts/zksync/facets/DiamondCut.sol

104:    /// @param _diamondCutHash The hash of the diamond cut that security council members want to approve. Needed to prevent unintentional approvals, including reorg attacks
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L14
```javascript
File: ethereum/contracts/zksync/libraries/Diamond.sol

14:     bytes32 constant DIAMOND_STORAGE_POSITION = 0xc8fcad8db84d3cc18b4c41d551ea0ee66dd599cde068d998e57d5e09332c131b; // keccak256("diamond.standard.diamond.storage") - 1;
```