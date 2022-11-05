### N01: EVENT IS MISSING INDEXED FIELDS
#### prof
zksync/libraries/Diamond.sol, 16, b'    event DiamondCut(FacetCut[] facetCuts, address initAddress, bytes initCalldata);'
zksync/interfaces/IExecutor.sol, 85, b'    event BlocksRevert(uint256 totalBlocksCommitted, uint256 totalBlocksVerified, uint256 totalBlocksExecuted);'
zksync/interfaces/IMailbox.sol, 101, b'    event NewPriorityRequest(\n        uint256 txId,\n        bytes32 txHash,\n        uint64 expirationBlock,\n        L2CanonicalTransaction transaction,\n        bytes[] factoryDeps\n    );'
zksync/interfaces/IDiamondCut.sol, 20, b'    event DiamondCutProposal(Diamond.FacetCut[] _facetCuts, address _initAddress);'
zksync/interfaces/IDiamondCut.sol, 24, b'    event DiamondCutProposalExecution(Diamond.DiamondCutData _diamondCut);'
zksync/interfaces/IDiamondCut.sol, 26, b'    event EmergencyFreeze();'
zksync/interfaces/IDiamondCut.sol, 28, b'    event Unfreeze(uint256 lastDiamondFreezeTimestamp);'
zksync/interfaces/IGovernance.sol, 32, b'    event IsPorterAvailableStatusUpdate(bool isPorterAvailable);'
zksync/interfaces/IGovernance.sol, 48, b'    event NewVerifierParams(VerifierParams oldVerifierParams, VerifierParams newVerifierParams);'

### N02: NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES
bridge/L1ERC20Bridge.sol, 3, b'pragma solidity ^0.8.0;'
bridge/L1EthBridge.sol, 3, b'pragma solidity ^0.8.0;'
bridge/interfaces/IL1Bridge.sol, 3, b'pragma solidity ^0.8.0;'
bridge/interfaces/IL2Bridge.sol, 3, b'pragma solidity ^0.8.0;'
dev-contracts/RevertFallback.sol, 3, b'pragma solidity ^0.8.0;'
dev-contracts/ReturnSomething.sol, 3, b'pragma solidity ^0.8.0;'
dev-contracts/TestnetERC20Token.sol, 3, b'pragma solidity ^0.8.0;'
dev-contracts/test/PriorityQueueTest.sol, 3, b'pragma solidity ^0.8.0;'
dev-contracts/test/DiamondProxyTest.sol, 3, b'pragma solidity ^0.8.0;'
dev-contracts/test/GovernanceFacetTest.sol, 3, b'pragma solidity ^0.8.0;'
dev-contracts/test/UnsafeBytesTest.sol, 3, b'pragma solidity ^0.8.0;'
dev-contracts/test/DummyERC20NoTransferReturnValue.sol, 3, b'pragma solidity ^0.8.0;'
dev-contracts/test/DummyERC20BytesTransferReturnValue.sol, 3, b'pragma solidity ^0.8.0;'
dev-contracts/test/DiamondCutTest.sol, 3, b'pragma solidity ^0.8.0;'
dev-contracts/test/MerkleTest.sol, 3, b'pragma solidity ^0.8.0;'
common/ReentrancyGuard.sol, 3, b'pragma solidity ^0.8.0;'
common/AllowList.sol, 3, b'pragma solidity ^0.8.0;'
common/L2ContractHelper.sol, 3, b'pragma solidity ^0.8.0;'
common/AllowListed.sol, 3, b'pragma solidity ^0.8.0;'
common/libraries/UnsafeBytes.sol, 3, b'pragma solidity ^0.8.0;'
common/libraries/UncheckedMath.sol, 3, b'pragma solidity ^0.8.0;'
common/interfaces/IAllowList.sol, 3, b'pragma solidity ^0.8.0;'
zksync/Verifier.sol, 3, b'pragma solidity ^0.8.0;'
zksync/Storage.sol, 3, b'pragma solidity ^0.8.0;'
zksync/Plonk4VerifierWithAccessToDNext.sol, 3, b'pragma solidity ^0.8.0;'
zksync/facets/Governance.sol, 3, b'pragma solidity ^0.8.0;'
zksync/facets/Base.sol, 3, b'pragma solidity ^0.8.0;'
zksync/facets/Getters.sol, 3, b'pragma solidity ^0.8.0;'
zksync/libraries/PairingsBn254.sol, 3, b'pragma solidity ^0.8.0;'
zksync/libraries/TranscriptLib.sol, 3, b'pragma solidity ^0.8.0;'
zksync/libraries/Diamond.sol, 3, b'pragma solidity ^0.8.0;'
zksync/libraries/Merkle.sol, 3, b'pragma solidity ^0.8.0;'
zksync/libraries/PriorityQueue.sol, 3, b'pragma solidity ^0.8.0;'
zksync/interfaces/IExecutor.sol, 3, b'pragma solidity ^0.8;'
zksync/interfaces/IMailbox.sol, 3, b'pragma solidity ^0.8;'
zksync/interfaces/IZkSync.sol, 3, b'pragma solidity ^0.8;'
zksync/interfaces/IDiamondCut.sol, 3, b'pragma solidity ^0.8;'
zksync/interfaces/IGetters.sol, 3, b'pragma solidity ^0.8.0;'
zksync/interfaces/IGovernance.sol, 3, b'pragma solidity ^0.8;'


### L01:_SAFEMINT() SHOULD BE USED RATHER THAN _MINT() WHEREVER POSSIBLE
#### problem
_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both OpenZeppelin and solmate have versions of this function
#### prof
dev-contracts/TestnetERC20Token.sol, 19, b'        _mint(_to, _amount);'

### L02: INPUT ARRAY LENGTHS MAY DIFFER
#### problem
If the caller makes a copy-paste error, the lengths may be mismatchd and an operation believed to have been completed may not in fact have been completed
function withdrawMultipleERC721(address[] memory _tokens, uint256[] memory _tokenId, address _to) external override {
#### prof
common/AllowList.sol, 72, b'    function setBatchPublicAccess(address[] calldata _targets, bool[] calldata _enables) external onlyOwner '
common/AllowList.sol, 106, b'    function setBatchPermissionToCall(\n        address[] calldata _callers,\n        address[] calldata _targets,\n        bytes4[] calldata _functionSigs,\n        bool[] calldata _enables\n    ) external onlyOwner '
common/interfaces/IAllowList.sol, 49, b'    function setBatchPublicAccess(address[] calldata _targets, bool[] calldata _enables) external;'
common/interfaces/IAllowList.sol, 58, b'    function setBatchPermissionToCall(\n        address[] calldata _callers,\n        address[] calldata _targets,\n        bytes4[] calldata _functionSigs,\n        bool[] calldata _enables\n    ) external;'

### L03: address variable should check if it is zero MISSING CHECKS FOR ADDRESS(0X0) WHEN ASSIGNING VALUES TO ADDRESS STATE VARIABLES
#### prof
bridge/L1ERC20Bridge.sol, 58, b'        zkSyncMailbox = _mailbox;'
bridge/L1ERC20Bridge.sol, 59, b'        allowList = _allowList;'
bridge/L1ERC20Bridge.sol, 79, b'        l2TokenFactory = _l2TokenFactory;'
bridge/L1ERC20Bridge.sol, 94, b'        l2Bridge = L2ContractHelper.computeCreate2Address(\n            address(this),\n            create2Salt,\n            l2BridgeBytecodeHash,\n            keccak256(create2Input)\n        );'
bridge/L1EthBridge.sol, 49, b'        zkSyncMailbox = _mailbox;'
bridge/L1EthBridge.sol, 50, b'        allowList = _allowList;'
bridge/L1EthBridge.sol, 70, b'        l2Bridge = L2ContractHelper.computeCreate2Address(\n            address(this),\n            create2Salt,\n            l2BridgeBytecodeHash,\n            keccak256(create2Input)\n        );'
common/AllowList.sol, 34, b'        owner = _owner;'
common/AllowList.sol, 146, b'            pendingOwner = _newPendingOwner;'
common/AllowList.sol, 158, b'            owner = newOwner;'