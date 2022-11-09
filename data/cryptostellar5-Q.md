
### L-01 REQUIRE() SHOULD BE USED INSTEAD OF ASSERT()

*Number of Instances Identified: 1*

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol

```
16: assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);
```


### L-02 FRONT-RUNNABLE INITIALIZERS

*Number of Instances Identified: 3*

All contract **initializers** were missing access controls, allowing any user to initialize the contract. By front-running the contract deployers to initialize the contract, the incorrect parameters may be supplied, leaving the contract needing to be redeployed.

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L71-L75

```
function initialize(
        bytes[] calldata _factoryDeps,
        address _l2TokenFactory,
        address _governor
    ) external reentrancyGuardInitializer {
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L56

```
function initialize(bytes calldata _l2BridgeBytecode) external reentrancyGuardInitializer {
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#L25-L37

```
 function initialize(
        Verifier _verifier,
        address _governor,
        address _validator,
        bytes32 _genesisBlockHash,
        uint64 _genesisIndexRepeatedStorageChanges,
        bytes32 _genesisBlockCommitment,
        IAllowList _allowList,
        VerifierParams calldata _verifierParams,
        bool _zkPorterIsAvailable, 
        bytes32 _l2BootloaderBytecodeHash,
        bytes32 _l2DefaultAccountBytecodeHash
    ) external reentrancyGuardInitializer returns (bytes32)
```


### L-03 FLOATING PRAGMA VERSION SHOULD NOT BE USED

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Proof of Concept

https://swcregistry.io/docs/SWC-103

This is applicable throughout the contracts

```
pragma solidity ^0.8.0;
```




### N-01 REQUIRE() STATEMENTS SHOULD HAVE DESCRIPTIVE REASON STRINGS

*Number of Instances Identified: 8*

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol

```
221: require(_message.length == 56);
224: require(bytes4(functionSignature) == this.finalizeWithdrawal.selector);
238: require(callSuccess);
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol

```
43: require(expectedNumberOfLayer1Txs == _newBlock.numberOfLayer1Txs);
45: require(l2BlockTimestamp == _newBlock.timestamp);
297: require(_recurisiveAggregationInput.length == 4);
```


https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol

```
50: require(_l1Token == CONVENTIONAL_ETH_ADDRESS);
```

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol

```
96: require(msg.sender == l2Bridge);
```


### N-02 EVENTS MISSING INDEXED FIELDS

*Number of Instances Identified: 7*

Each `event` should use three `indexed` fields if there are three or more fields

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/interfaces/IL1Bridge.sol

```
11: event WithdrawalFinalized(address indexed to, address indexed l1Token, uint256 amount);
13: event ClaimedFailedDeposit(address indexed to, address indexed l1Token, uint256 amount);
```


https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol

```
20:  event DiamondCutProposal(Diamond.FacetCut[] _facetCuts, address _initAddress); //@audit-issue MISSING INDEXED FIELDS
22: event DiamondCutProposalCancelation(uint256 currentProposalId, bytes32 indexed proposedDiamondCutHash);
24: event DiamondCutProposalExecution(Diamond.DiamondCutData _diamondCut);
28: event Unfreeze(uint256 lastDiamondFreezeTimestamp);
```


https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol


```
85: event BlocksRevert(uint256 totalBlocksCommitted, uint256 totalBlocksVerified, uint256 totalBlocksExecuted);
```

### N-03 USE LATEST SOLIDITY VERSION

This is applicable to all inscope contracts.

The contracts use pragma solidity version as follows:

```
pragma solidity ^0.8.0;
```


### N-04 NATSPEC IS INCOMPLETE


missing @params.
This is also applicable throughout the smart contracts. Every functions should have well defined @params , @dev etc

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L43

```
43: function bridgeInitialize(address _l1Address, bytes memory _data) external initializer
```
