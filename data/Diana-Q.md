
## L-01 REQUIRE() SHOULD BE USED INSTEAD OF ASSERT()

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol

```
File: contracts/zksync/facets/DiamondCut.sol

16: assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);
```

---------------

## L-02 NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Proof of Concept

This issue exists on all In-scope contracts 

### Recommended Mitigation Steps

Lock the pragma version

-------------

## N-01 EVENT IS MISSING INDEXED FIELDS

Each `event` should use three `indexed` fields if there are three or more fields

_There are 7 instances of this issue_

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol

```
File: contracts/zksync/interfaces/IDiamondCut.sol

20:  event DiamondCutProposal(Diamond.FacetCut[] _facetCuts, address _initAddress);
22: event DiamondCutProposalCancelation(uint256 currentProposalId, bytes32 indexed proposedDiamondCutHash);
24: event DiamondCutProposalExecution(Diamond.DiamondCutData _diamondCut);
28: event Unfreeze(uint256 lastDiamondFreezeTimestamp);
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol

```
File: contracts/zksync/interfaces/IExecutor.sol

85: event BlocksRevert(uint256 totalBlocksCommitted, uint256 totalBlocksVerified, uint256 totalBlocksExecuted);
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/interfaces/IL1Bridge.sol

```
File: contracts/bridge/interfaces/IL1Bridge.sol

11: event WithdrawalFinalized(address indexed to, address indexed l1Token, uint256 amount);
13: event ClaimedFailedDeposit(address indexed to, address indexed l1Token, uint256 amount);
```

------------

## N-02 USE A MORE RECENT VERSION OF SOLIDITY

### Proof of Concept

This issue exists on all In-scope contracts 

For instance:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol

```
File: contracts/common/L2ContractHelper.sol

3: pragma solidity ^0.8.0;
```

----------

## N-03 REQUIRE() STATEMENTS SHOULD HAVE DESCRIPTIVE REASON STRINGS

_There are 8 instances of this issue_

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol

```
File: contracts/bridge/L1EthBridge.sol

221: require(_message.length == 56);
224: require(bytes4(functionSignature) == this.finalizeWithdrawal.selector);
238: require(callSuccess);
```

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol

```
File: contracts/bridge/L2StandardERC20.sol

96: require(msg.sender == l2Bridge);
```

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol

```
File: contracts/zksync/facets/Executor.sol

43: require(expectedNumberOfLayer1Txs == _newBlock.numberOfLayer1Txs);
45: require(l2BlockTimestamp == _newBlock.timestamp);
297: require(_recurisiveAggregationInput.length == 4);
```

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol

```
File: contracts/bridge/L2ETHBridge.sol

50: require(_l1Token == CONVENTIONAL_ETH_ADDRESS);
```

-----------------

