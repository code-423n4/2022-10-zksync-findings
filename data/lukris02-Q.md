# QA Report for zkSync v2 contest
## Overview
During the audit, 1 low and 8 non-critical issues were found.
№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
L-1 | Missing check for zero address | Low | 7
NC-1 | Order of Functions | Non-Critical | 10
NC-2 | Order of Layout | Non-Critical | 2
NC-3 | Public functions can be external | Non-Critical | 2
NC-4 | Open TODOs | Non-Critical | 5
NC-5 | Typos in function name / argument name / struct name | Non-Critical | 3
NC-6 | Typos | Non-Critical | 10
NC-7 | No error message in ```require``` | Non-Critical | 9
NC-8 | Missing NatSpec | Non-Critical | 13

## Low Risk Findings(1)
### L-1. Missing check for zero address
##### Description
If address(0x0) is set it may cause the contract to revert or work wrong.
##### Instances
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#L27
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#L28
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L15
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L73
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L74
- https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L32
- https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L30

##### Recommendation
Add checks.
#
## Non-Critical Risk Findings(8)
### NC-1. Order of Functions
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-functions), ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier.  
Functions should be grouped according to their visibility and ordered:
1) constructor
2) receive function (if exists)
3) fallback function (if exists)
4) external
5) public
6) internal
7) private
##### Instances
exteranl functions between internal:
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L152
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L208-L271
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L336
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L179-L257
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L140-L211
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L94-L120
- https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L91

public function between/after internal:
- https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L113
- https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L113
- https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L79-L85

##### Recommendation
Reorder functions where possible.
#
### NC-2. Order of Layout
##### Description
According to [Order of Layout](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout), inside each contract, library or interface, use the following order:
1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions

##### Instances 
structs should be placed before event:
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L22-L77

modifier should be placed before constructor:
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L37

#
### NC-3. Public functions can be external
##### Description
If functions are not called by the contract where they are defined, they can be declared external.
##### Instances
- https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L79
- https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L84

##### Recommendation
Make public functions external, where possible.
#
### NC-4. Open TODOs
##### Instances
- [```// TODO: change constant to the real root hash of empty Merkle tree (SMA-184)```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L28) 
- [```// TODO: estimate gas for L1 execute```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L94) 
- [```// TODO: Restore after stable priority op fee modeling. (SMA-1230)```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L127) 
- [```layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L169) 
- [```/// TODO: The verifier integration is not finished yet, change the structure for compatibility later```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L56) 

##### Recommendation
Resolve issues.
#
### NC-5. Typos in function name / argument name / struct name
##### Instances
- [```function _verifyRecursivePartOfProof(uint256[] calldata _recurisiveAggregationInput) {```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L296) => ```recursive``` (argument)
- [```function _blockAuxilaryOutput```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L376) => ```Auxiliary```
- [```uint256[] recurisiveAggregationInput;```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L58)  => ```recursive```

#
### NC-6. Typos
##### Instances
- [```/// @title Diamond Proxy Cotract (EIP-2535)```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L7)=> ```Contract```
- [```/// @dev The sender is an `address` type, although we are using `uint256` for addreses in `L2CanonicalTransaction`.```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L36)=> ```addresses```
- [```/// @param initAddress The address that's dellegate called after setting up new facet changes```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L63) => ```delegate```
- [```/// @param initCalldata Calldata for the delegete call to `initAddress```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L64) => ```delegate```
- [```/// NOTE: It is expected but NOT enforced that there are no selectors associated wih `_facet```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L255) => ```with```
- [```/// @dev It is standard implementation of ERC20 Bridge that can be used as a refference```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L19) => ```reference```
- [```// We are expecting to see the exect two bytecodes that are needed to initiailize the bridge```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L76) => ```initialize```
- [```// Save the deposit amount, to claim funds back if the L2 transaction will failed```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L106) => ```transaction failed``` (without ```will```)
- [```/// @notice Address of the L2 token by its L1 couterpart```](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L78) => ```counterpart```
- [```/// @dev Stores the L1 address of the bridge and set `name`/`symbol`/`decimls` getters that L1 token has.```](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L42) => ```decimals```

#
### NC-7. No error message in ```require```
##### Instances
- [```require(expectedNumberOfLayer1Txs == _newBlock.numberOfLayer1Txs);```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L43) 
- [```require(l2BlockTimestamp == _newBlock.timestamp);```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L45) 
- [```require(_recurisiveAggregationInput.length == 4);```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L297) 
- [```require(amount != 0);```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L145) 
- [```require(_message.length == 56);```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L221) 
- [```require(bytes4(functionSignature) == this.finalizeWithdrawal.selector);```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L224) 
- [```require(callSuccess);```](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L238) 
- [```require(_l1Token == CONVENTIONAL_ETH_ADDRESS);```](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L50) 
- [```require(msg.sender == l2Bridge);```](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L96) 

##### Recommendation
Add error messages.
#
### NC-8. Missing NatSpec
##### Description
NatSpec is missing for 13 functions in 5 contracts.
##### Instances
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L80
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L362
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L372
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L376
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L116
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L62
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L65
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L71
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L73
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/ReentrancyGuard.sol#L43
- https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L114
- https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L120
- https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L126

##### Recommendation
Add NatSpec for all functions.