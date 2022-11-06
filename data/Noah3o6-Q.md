-> MISSING INDEXED FIELDS IN EVENT

Fields, that are indexed, are more quickly accessible to off-chain tools that parse events. Every indexed field cost extra gas.
If there are three or more fields in an event you should use at least 3 or more indexed fields. When there are less than three fields all should be indexed.

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#:~:text=event%20DiamondCut(FacetCut%5B%5D%20facetCuts%2C%20address%20initAddress%2C%20bytes%20initCalldata)%3B
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#:~:text=event%20EmergencyDiamondCutApproved(,)%3B
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#:~:text=event%20BlocksRevert(uint256%20totalBlocksCommitted%2C%20uint256%20totalBlocksVerified%2C%20uint256%20totalBlocksExecuted)%3B
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IGovernance.sol#:~:text=event%20IsPorterAvailableStatusUpdate(bool%20isPorterAvailable)%3B
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IGovernance.sol#:~:text=event%20ValidatorStatusUpdate(address%20indexed%20validatorAddress%2C%20bool%20isActive)%3B
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IGovernance.sol#:~:text=event%20NewVerifierParams(VerifierParams%20oldVerifierParams%2C%20VerifierParams%20newVerifierParams)%3B
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IMailbox.sol#:~:text=event%20NewPriorityRequest(,)%3B
https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#:~:text=event%20BridgeInitialization(address%20indexed%20l1Token%2C%20string%20name%2C%20string%20symbol%2C%20uint8%20decimals)%3B
https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/interfaces/IL2StandardToken.sol#:~:text=event%20BridgeMint(address%20indexed%20_account%2C%20uint256%20_amount)%3B
https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/interfaces/IL2StandardToken.sol#:~:text=event%20BridgeBurn(address%20indexed%20_account%2C%20uint256%20_amount)%3B


->USE NEWER VERSION OF SOLIDITY

Use a newer solidity version of at least 0.8.10, so you have at least external calls skip contract existence, it checks if the external call has a return value

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#:~:text=pragma%20solidity%20%5E0.8.0%3B
https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/interfaces/IL2StandardToken.sol#:~:text=pragma%20solidity%20%5E0.8.0%3B
