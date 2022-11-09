1)X = X + Y IS CHEAPER THAN X += Y  

File: 2022-10-zksync\ethereum\contracts\zksync\facets\DiamondCut.sol
  29,47:         s.diamondCutStorage.currentProposalId += 1;
  

2)USE A MORE RECENT VERSION OF SOLIDITY

Use a solidity version of at least 0.8.2 to get compiler automatic inlining

Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads

Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings

Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value



File: 2022-10-zksync\ethereum\contracts\bridge\L1ERC20Bridge.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-zksync\ethereum\contracts\bridge\L1EthBridge.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-zksync\ethereum\contracts\bridge\interfaces\IL1Bridge.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-zksync\ethereum\contracts\bridge\interfaces\IL2Bridge.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  


File: 2022-10-zksync\ethereum\contracts\zksync\Config.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-zksync\ethereum\contracts\zksync\DiamondInit.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-zksync\ethereum\contracts\zksync\DiamondProxy.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  


File: 2022-10-zksync\ethereum\contracts\zksync\Storage.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  
 

File: 2022-10-zksync\ethereum\contracts\zksync\facets\Base.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-zksync\ethereum\contracts\zksync\facets\DiamondCut.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-zksync\ethereum\contracts\zksync\facets\Executor.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-zksync\ethereum\contracts\zksync\facets\Getters.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-zksync\ethereum\contracts\zksync\facets\Governance.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-zksync\ethereum\contracts\zksync\facets\Mailbox.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-zksync\ethereum\contracts\zksync\interfaces\IDiamondCut.sol:
  2  
  3: pragma solidity ^0.8;
  4  

File: 2022-10-zksync\ethereum\contracts\zksync\interfaces\IExecutor.sol:
  2  
  3: pragma solidity ^0.8;
  4  

File: 2022-10-zksync\ethereum\contracts\zksync\interfaces\IGetters.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-zksync\ethereum\contracts\zksync\interfaces\IGovernance.sol:
  2  
  3: pragma solidity ^0.8;
  4  

File: 2022-10-zksync\ethereum\contracts\zksync\interfaces\IMailbox.sol:
  2  
  3: pragma solidity ^0.8;
  4  

File: 2022-10-zksync\ethereum\contracts\zksync\interfaces\IZkSync.sol:
  2  
  3: pragma solidity ^0.8;
  4  

3)Splitting require() statements that use && saves gas


File: 2022-10-zksync\ethereum\contracts\common\AllowList.sol:
  96          require(
  97:             callersLength == _targets.length &&
  98:                 callersLength == _functionSigs.length &&
  99                  callersLength == _enables.length,
 100:               "yw"
 101:             );
 
4)Use assembly to check for address(0)
Impact

File: 2022-10-zksync\ethereum\contracts\common\AllowList.sol
  33,27:         require(_owner != address(0), "kq");
  161,44:             emit NewPendingOwner(newOwner, address(0));
  
File: 2022-10-zksync\ethereum\contracts\zksync\DiamondProxy.sol
  29,33:         require(facetAddress != address(0), "F"); // Proxy has no facet for this selector 
  
  
File: 2022-10-zksync\ethereum\contracts\zksync\facets\Getters.sol
  148,63:         require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");

File: 2022-10-zksync\ethereum\contracts\zksync\libraries\Diamond.sol
  126,27:         require(_facet != address(0), "G"); // facet with zero address cannot be added
  135,46:             require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists
  150,27:         require(_facet != address(0), "K"); // cannot replace facet with zero address
  156,46:             require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address
  170,27:         require(_facet == address(0), "a1"); // facet address must be zero
  176,46:             require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet
  278,22:         if (_init == address(0)) {  



5)Multiple address mappings can be combined into a single mapping of an address to a struct

Multiple address mappings can be combined into a single mapping of an address to a struct,
where appropriate saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.


File: 2022-10-zksync\ethereum\contracts\bridge\L1ERC20Bridge.sol:
  37  
  38:     /// @dev A mapping L2 block number => message number => flag
  39      /// @dev Used to indicate that zkSync L2 -> L1 message was already processed
  40:     mapping(uint256 => mapping(uint256 => bool)) public isWithdrawalFinalized;
  41  
  42:     /// @dev A mapping account => L1 token address => L2 deposit transaction hash => amount
  43      /// @dev Used for saving the number of deposited funds, to claim them in case the deposit transaction will fail
  44:     mapping(address => mapping(address => mapping(bytes32 => uint256))) depositAmount;
  45  

File: 2022-10-zksync\ethereum\contracts\bridge\L1EthBridge.sol:
  34  
  35:     /// @dev A mapping L2 block number => message number => flag
  36      /// @dev Used to indicate that zkSync L2 -> L1 message was already processed
  37:     mapping(uint256 => mapping(uint256 => bool)) public isWithdrawalFinalized;
  38  
  39:     /// @dev A mapping account => L1 token address => L2 deposit transaction hash => amount
  40      /// @dev Used for saving the number of deposited funds, to claim them in case the deposit transaction will fail
  41:     mapping(address => mapping(bytes32 => uint256)) depositAmount;
  42  

File: 2022-10-zksync\ethereum\contracts\common\AllowList.sol:
  24  
  25:     /// @notice mapping of the addresses that everyone has permission to call
  26:     mapping(address => bool) public isAccessPublic;
  27  
  28:     /// @notice The mapping that stores permissions to call the function on the target address by the caller
  29      /// @dev caller => target => function signature => permission to call target function for the given caller address
  30:     mapping(address => mapping(address => mapping(bytes4 => bool))) public hasSpecialAccessToCall;
  31  

File: 2022-10-zksync\ethereum\contracts\zksync\Storage.sol:
  14  /// @param securityCouncilMembers The set of the trusted addresses that can instantly finish upgrade (diamond cut)
  15: /// @param securityCouncilMemberLastApprovedProposalId The mapping of the security council addresses and the last diamond cut that they approved
  16  /// @param securityCouncilEmergencyApprovals The number of received upgrade approvals from the security council

  21      uint256 currentProposalId;
  22:     mapping(address => bool) securityCouncilMembers;
  23:     mapping(address => uint256) securityCouncilMemberLastApprovedProposalId;
  24      uint256 securityCouncilEmergencyApprovals;

  76      /// @notice List of permitted validators
  77:     mapping(address => bool) validators;
  78      /// @dev Verifier contract. Used to verify aggregated proof for blocks

  86      /// @dev Stored hashed StoredBlock for block number
  87:     mapping(uint256 => bytes32) storedBlockHashes;
  88      /// @dev Stored root hashes of L2 -> L1 logs
  89:     mapping(uint256 => bytes32) l2LogsRootHashes;
  90      /// @dev Container that stores transactions requested from L1



  
  
