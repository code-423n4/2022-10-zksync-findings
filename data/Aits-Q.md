###  ` Staking.acceptMigrationTarget() ` Unnecessary ` If ` statements. 

Here, as the  `onlyOwner ` modifier is applied, the ` address(0) ` checks are not needed here:


https://github.com/code-423n4/2022-11-chainlink/blob/main/contracts/Staking.sol#L432-L434

```
     function acceptMigrationTarget() external override(IMigratable) onlyOwner {
     if (address(s_proposedMigrationTarget) == address(0))
      revert InvalidMigrationTarget();

```