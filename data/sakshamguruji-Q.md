## VIOLATION OF CHECKS-EFFECTS-INTERACTION PATTERN

## Description:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L80-L83
Here the checks-effect-interaction pattern is violated , the correct sequence of instructions should be,
```
require(!diamondStorage.isFrozen, "a9"); // diamond proxy is frozen already
        diamondStorage.isFrozen = true;
        _resetProposal();

```

Similarly, here https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L94-L98
the correct sequence should be 
```
require(diamondStorage.isFrozen, "a7"); // diamond proxy is not frozen
    
        diamondStorage.isFrozen = false;
        _resetProposal();

        
```


## EVENT IS MISSING INDEXED FIELDS

### Description:

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index
field costs extra gas during emission, so it’s not necessarily best to index the maximum allowed per event (three fields).
Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for
the events in question. If there are fewer than three fields, all of the fields should be indexed.

Instances of this issue:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L16
https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L12

## INTERNAL FUNCTIONS ARE MISSING UNDERSCORE

### Description:

As a best practice, it is advised to start the internal function names with an underscore e.g. _someInternalFunction.

Instances of this issue:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L89
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L80
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol#L17
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L34
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L39
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L44
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L49
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L54
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L63
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L71

## MISSING CHECK ON setVerifierParams FUNCTION

### Description:

The setVerifierParams function on 
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L104
misses a check i.e. `oldverifierParams!= newVerifierParams`  , currently without this check the event NewVerifierParams on 
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L104 would be emitted even if the new params and old params were the same. 
Make this change similar to the one here https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L96


## INHERITING CONTRACTS WHICH ARE NOT USED

Description:
The contract Base.sol inherits the contract ReentrancyGuard.sol but does not use its functionality , this should be avoided.

## PUBLIC FUNCTIONS NOT CALLED WITHIN THE CONTRACT SHOULD BE DECLARED EXTERNAL INSTEAD

###  Description

The function here https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L89-L95 should be 
declared external instead , eventhough the comments say it would be called within the contract in the future , this should be taken into account.

More instances:
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L282
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L243

## UNNECESSARY COMPLEXITY IN CODE

### Description:

In this function https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L136-L145 
rather than returning `balanceAfter - balanceBefore;` we can just return the _amount and even no need to calculate the `balanceBefore` and
`balanceAfter` which might also save us some gas.







