## Table of Contents
### Low Risk Issues
- Missing Zero Address Check 
- Require should be used instead of Assert

### Non-critical Issues
- Event is Missing Indexed Fields
- Should Resolve TODOs before Deployment
- Require Statements without Descriptive Revert Strings

&ensp;
## Low Risk Issues
### Missing Zero Address Check 

#### Issue
I recommend adding check of 0-address for input validation of critical address parameters.
Not doing so might lead to non-functional contract and have to redeploy the contract, when it is updated to 0-address accidentally.

#### PoC
Total of 7 instances found.

1. L1ERC20Bridge.sol:constructor():  `allowList` address 
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L59

2. L1ERC20Bridge.sol:constructor():  `zkSyncMailbox` address 
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L58

3. L1ERC20Bridge.sol:initialize():  `l2TokenFactory` address 
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L79

4. L1EthBridge.sol:constructor():  `allowList` address 
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L50

5. L1EthBridge.sol:constructor():  `zkSyncMailbox` address 
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L49

6. L2ERC20Bridge.sol:constructor():  `l1Bridge` address 
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ERC20Bridge.sol#L36

7. L2ETHBridge.sol:constructor():  `l1Bridge` address 
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ETHBridge.sol#L31

#### Mitigation
Add 0-address check for above addresses.

&ensp;
### Require should be used instead of Assert

#### Issue
Solidity documents mention that properly functioning code should never reach a failing assert statement
and if this happens there is a bug in the contract which should be fixed.
Reference: https://docs.soliditylang.org/en/v0.8.15/control-structures.html#panic-via-assert-and-error-via-require

#### PoC
Total of 1 instance found.
```
./DiamondCut.sol:16:        assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);
```

#### Mitigation
Replace assert by require.

&ensp;
## Non-critical Issues
### Event is Missing Indexed Fields

#### Issue
Each event should have 3 indexed fields if there are 3 or more fields.

#### PoC
Total of 17 instances found.
```solidity
./IAllowList.sol:11:    event UpdatePublicAccess(address indexed target, bool newStatus);
./ethereum/contracts/bridge/interfaces/IL1Bridge.sol:11:    event WithdrawalFinalized(address indexed to, address indexed l1Token, uint256 amount);
./ethereum/contracts/bridge/interfaces/IL1Bridge.sol:13:    event ClaimedFailedDeposit(address indexed to, address indexed l1Token, uint256 amount);
./IExecutor.sol:85:    event BlocksRevert(uint256 totalBlocksCommitted, uint256 totalBlocksVerified, uint256 totalBlocksExecuted);
./IMailbox.sol:95:    event NewPriorityRequest(
                          uint256 txId,
                          bytes32 txHash,
                          uint64 expirationBlock,
                          L2CanonicalTransaction transaction,
                          bytes[] factoryDeps
                      );
./IDiamondCut.sol:20:    event DiamondCutProposal(Diamond.FacetCut[] _facetCuts, address _initAddress);
./IDiamondCut.sol:22:    event DiamondCutProposalCancelation(uint256 currentProposalId, bytes32 indexed proposedDiamondCutHash);
./IDiamondCut.sol:24:    event DiamondCutProposalExecution(Diamond.DiamondCutData _diamondCut);
./IDiamondCut.sol:28:    event Unfreeze(uint256 lastDiamondFreezeTimestamp);
./IDiamondCut.sol:30:    event EmergencyDiamondCutApproved(
                             address indexed _address,
                             uint256 currentProposalId,
                             uint256 securityCouncilEmergencyApprovals,
                             bytes32 indexed proposedDiamondCutHash
                         );
./Diamond.sol:16:    event DiamondCut(FacetCut[] facetCuts, address initAddress, bytes initCalldata);
./IGovernance.sol:32:    event IsPorterAvailableStatusUpdate(bool isPorterAvailable);
./IGovernance.sol:35:    event ValidatorStatusUpdate(address indexed validatorAddress, bool isActive);
./IGovernance.sol:48:    event NewVerifierParams(VerifierParams oldVerifierParams, VerifierParams newVerifierParams);
./IL2StandardToken.sol:6:    event BridgeMint(address indexed _account, uint256 _amount);
./IL2StandardToken.sol:8:    event BridgeBurn(address indexed _account, uint256 _amount);
./L2StandardERC20.sol:12:    event BridgeInitialization(address indexed l1Token, string name, string symbol, uint8 decimals);
```

#### Mitigation
Add up to 3 indexed fields when possible.

&ensp;
### Should Resolve TODOs before Deployment

#### Issue
Questions/Issues in the code should be resolved before the deployment.

### PoC
Total of 5 instances found
```
./IExecutor.sol:56:    /// TODO: The verifier integration is not finished yet, change the structure for compatibility later
./Config.sol:28:// TODO: change constant to the real root hash of empty Merkle tree (SMA-184)
./Mailbox.sol:94:        // TODO: estimate gas for L1 execute
./Mailbox.sol:127:        // TODO: Restore after stable priority op fee modeling. (SMA-1230)
./Mailbox.sol:169:                layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)
```

&ensp;
### Require Statements without Descriptive Revert Strings

#### Issue
It is best practice to include descriptive revert strings for require statement for readability and auditing.

#### PoC
Total of 9 instances found.
```solidity
./L2ETHBridge.sol:50:        require(_l1Token == CONVENTIONAL_ETH_ADDRESS);
./L2StandardERC20.sol:96:        require(msg.sender == l2Bridge);
./L1EthBridge.sol:145:        require(amount != 0);
./L1EthBridge.sol:221:        require(_message.length == 56);
./L1EthBridge.sol:224:        require(bytes4(functionSignature) == this.finalizeWithdrawal.selector);
./L1EthBridge.sol:238:        require(callSuccess);
./Executor.sol:43:        require(expectedNumberOfLayer1Txs == _newBlock.numberOfLayer1Txs);
./Executor.sol:45:        require(l2BlockTimestamp == _newBlock.timestamp);
./Executor.sol:297:        require(_recurisiveAggregationInput.length == 4);
```

#### Mitigation
Add descriptive revert strings to easier understand what the code is trying to do.

&ensp;