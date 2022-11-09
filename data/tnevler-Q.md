# Report
## Low Risk ##
### [L-1]: Missing checks for address(0x0)
**Context:**

1. ```s.governor = _governor;``` [L39](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#L39)
2. ``` s.validators[_validator] = true;``` [L40](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#L40)
3. ```s.pendingGovernor = _newPendingGovernor;``` [L21](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L21)
4. ```l2TokenFactory = _l2TokenFactory;``` [L79](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L79)
5. ```l1Bridge = _l1Bridge;``` [L36](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L36) 
6. ```l1Bridge = _l1Bridge;``` [L31](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L31) 

**Recommendation:**

Add non-zero address checks when set address state variables.

## Non-Critical Issues ##
### [N-1]: Function defines a named return variable but then it uses return statements
**Context:**

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L67-L77 

**Recommendation:**

Choose named return variable or return statement. It is unnecessary to use both.

### [N-2]: Wrong order of functions
**Context:**

1. [Executor.commitBlocks](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L152), [Executor.executeBlocks](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L208), [Executor.proveBlocks](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L221), [Executor.revertBlocks](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L336) (external functions must be before all public, internal, and private functions)
1. [Mailbox.l2TransactionBaseCost](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L89) (public function can not go after internal function)
1. [L1ERC20Bridge.claimFailedDeposit](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L179) (external function can not go after internal function)
1. [L1EthBridge.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol) (all internal functions must be after all external functions)
1. [AllowList.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol) (all internal functions must be after all external functions)
1. [L2ERC20Bridge.withdraw](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L87) (external function can not go after internal function)
1. [L2ETHBridge.l2TokenAddress](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L79) (public function can not go after internal function)

**Description:**

According to [official solidity documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) functions should be grouped according to their visibility and ordered:

+ constructor

+ receive function (if exists)

+ fallback function (if exists)

+ external

+ public

+ internal

+ private

Within a grouping, place the view and pure functions last.

**Recommendation:**

Put the functions in the correct order according to the documentation.

### [N-3]: Public functions can be external
**Context:**

1. [Mailbox.l2TransactionBaseCost](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L89)
1. [L2ETHBridge.l2TokenAddress](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L79) 
1. [L2ETHBridge.l1TokenAddress](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L84) 

**Description:**

Public functions can be declared external if they are not called by the contract.

**Recommendation:**

Declare these functions as external instead of public.

### [N-4]: Require() statements should have descriptive reason string
**Context:**

1. ```require(expectedNumberOfLayer1Txs == _newBlock.numberOfLayer1Txs);``` [L43](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L43) 
1. ```require(l2BlockTimestamp == _newBlock.timestamp);``` [L45](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L45) 
1. ```require(_recurisiveAggregationInput.length == 4);``` [L297](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L297) 
1. ```require(amount != 0);``` [L145](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L145) 
1. ```require(_message.length == 56);``` [L221](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L221) 
1. ```require(bytes4(functionSignature) == this.finalizeWithdrawal.selector);``` [L224](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L224) 
1. ```require(callSuccess);``` [L238](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L238) 
1. ```require(_l1Token == CONVENTIONAL_ETH_ADDRESS);``` [L50](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L50) 
1. ```require(msg.sender == l2Bridge);``` [L96](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L96) 

### [N-5]: NatSpec is missing
**Context:**

1. [Executor._calculateBlockHash](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L80) 
1. [Executor._blockPassThroughData](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L362) 
1. [Executor._blockMetaParameters](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L372) 
1. [Executor._blockAuxilaryOutput](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L376) 
1. [Mailbox._requestL2Transaction](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L116) 
1. [IDiamondCut.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol)
1. [IExecutor.commitBlocks](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L62) 
1. [IExecutor.proveBlocks](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L65) 
1. [IExecutor.executeBlocks](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L71) 
1. [IExecutor.revertBlocks](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L73) 
1. [IGetters.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IGetters.sol)
1. [IGovernance.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IGovernance.sol)
1. [IMailbox.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IMailbox.sol)
1. [IZkSync.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IZkSync.sol)
1. [IL1Bridge.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/interfaces/IL1Bridge.sol)
1. [IL2Bridge.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/interfaces/IL2Bridge.sol)
1. [L2ContractHelper.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol)
1. [ReentrancyGuard._initializeReentrancyGuard](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/ReentrancyGuard.sol#L43) 
1. [UncheckedMath.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/libraries/UncheckedMath.sol)
1. [UnsafeBytes.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/libraries/UnsafeBytes.sol)
1. [IAllowList.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/interfaces/IAllowList.sol)
1. [L2StandardERC20.name](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L114) 
1. [L2StandardERC20.symbol](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L120) 
1. [L2StandardERC20.decimals](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L126) 
1. [IL1Bridge.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/interfaces/IL1Bridge.sol)
1. [IL2Bridge.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/interfaces/IL2Bridge.sol)
1. [IL2EthInitializable.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/interfaces/IL2EthInitializable.sol)
1. [L2ContractHelper.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/L2ContractHelper.sol)

### [N-6]: Typos
**Context:**

1. ```/// @title Diamond Proxy Cotract (EIP-2535)``` [L7](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L7) (change ***Cotract*** to ***Contract***)
1. ```/// @dev The sender is an `address` type, although we are using `uint256` for addreses in `L2CanonicalTransaction`.``` [L36](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L36) (change ***addreses*** to ***addresses***)
1. ```// Check that block contain all meta information for L2 logs.``` [L30](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L30) (change ***contain*** to ***contains***)
1. ```/// @param initAddress The address that's dellegate called after setting up new facet changes``` [L63](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L63) (change ***dellegate*** to ***delegate***)
1. ```/// @param initCalldata Calldata for the delegete call to 'initAddress'``` [L64](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L64) (change ***delegete*** to ***delegate***)
1. ```/// NOTE: It is expected but NOT enforced that there are no selectors associated wih '_facet'``` [L255](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L255) (change ***wih*** to ***with***)
1. ```/// @dev It is standard implementation of ERC20 Bridge that can be used as a refference``` [L19](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L19) (change ***refference*** to ***reference***)
1. ```// We are expecting to see the exect two bytecodes that are needed to initiailize the bridge``` [L76](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L76) (change ***exect*** to ***exact***)
1. ```// We are expecting to see the exect two bytecodes that are needed to initiailize the bridge``` [L76](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L76) (change ***initiailize*** to ***initialize***)
1. ```// Save the deposit amount, to claim funds back if the L2 transaction will failed``` [L106](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L106) (change ***will failed*** to ***will fail*** or ***will be failed***)
1. ```/// @notice Address of the L2 token by its L1 couterpart``` [L78](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L78) (change ***couterpart*** to ***counterpart***)
1. ```/// @dev Stores the L1 address of the bridge and set `name`/`symbol`/`decimls` getters that L1 token has.``` [L42](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L42) (change ***decimls*** to ***decimals***)

### [N-7]: TODO
**Context:**

1. ```// TODO: change constant to the real root hash of empty Merkle tree (SMA-184)``` [L28](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L28) 
1. ```// TODO: estimate gas for L1 execute``` [L94](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L94) 
1. ```// TODO: Restore after stable priority op fee modeling. (SMA-1230)``` [L127](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L127) 
1. ```layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)``` [L169](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L169) 
1. ```/// TODO: The verifier integration is not finished yet, change the structure for compatibility later``` [L56](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L56) 

### [N-8]: Line is too long
**Context:**

1. ```/// @dev Logically separated part of the storage structure, which is responsible for everything related to proxy upgrades and diamond cuts``` [L9](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L9) 
1. ```/// @param securityCouncilMemberLastApprovedProposalId The mapping of the security council addresses and the last diamond cut that they approved``` [L15](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L15) 
1. ```/// @notice Total number of executed blocks i.e. blocks[totalBlocksExecuted] points at the latest executed block (block 0 is genesis)``` [L80](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L80) 
1. ```/// @param _diamondCutHash The hash of the diamond cut that security council members want to approve. Needed to prevent unintentional approvals, including reorg attacks``` [L104](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L104) 
1. ```require(s.diamondCutStorage.securityCouncilMemberLastApprovedProposalId[msg.sender] < currentProposalId, "ao"); // already approved this proposal``` [L108](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L108) 
1. ```require(s.diamondCutStorage.proposedDiamondCutHash == _diamondCutHash, "f1"); // proposed diamond cut do not match to the approved``` [L112](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L112) 
1. ```/// @param _message Information about the sent message: sender address, the message itself, tx index in the L2 block where the message was sent``` [L23](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L23) 
1. ```bytes32 constant DIAMOND_STORAGE_POSITION = 0xc8fcad8db84d3cc18b4c41d551ea0ee66dd599cde068d998e57d5e09332c131b; // keccak256("diamond.standard.diamond.storage") - 1;``` [L14](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L14) 
1. ```// - l2ShardId = 0 (means that L1 -> L2 transaction was processed in a rollup shard, other shards are not available yet anyway)``` [L196](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L196) 
1. ```// It should be equal to the length of the function signature + address + address + uint256 = 4 + 20 + 20 + 32 = 76 (bytes).``` [L270](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L270) 
1. ```/// @param _enables The array of boolean flags, whether enable or disable the public access to the corresponding target address``` [L64](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L64) 
1. ```/// @param _enables The array of boolean flags, whether enable or disable the function access to the corresponding target address``` [L88](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L88) 

**Description:**

Maximum suggested line length is [120 characters](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length).
