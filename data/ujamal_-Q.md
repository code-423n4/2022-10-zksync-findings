
Low Risk issues:

L-01: MISSING CHECKS FOR ADDRESS(0X0)
 WHEN ASSIGNING VALUES TO STATE VARIABLES

file path: contracts/common/AllowList.sol 
file path: contracts/zksync/facets/Governance.sol

    pendingOwner = _newPendingOwner;

    address oldPendingGovernor = s.pendingGovernor;
    address pendingGovernor = s.pendingGovernor;

Gas:

G01
contracts/zksync/facets/Executor.sol
 s.totalBlocksCommitted is accessed multiple times in a method Caching this in memory can save a sload, there are 2 instances of it in Executor.sol

G02:
Using the indexed keyword for value types such as uint, bool, and address saves gas costs
     
    event DiamondCut(FacetCut[] facetCuts, address initAddress, bytes initCalldata);
 

