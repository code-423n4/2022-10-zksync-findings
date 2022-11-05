## USE A MORE RECENT VERSION OF SOLIDITY

Use a solidity version of at least 0.8.0 to get overflow protection without `LowGasSafeMath` Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

Including all files in the scope.

## Always put your `require` statement on top in the function
[`ethereum/contracts/zksync/DiamondProxy.sol:24`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L24)        
[`ethereum/contracts/zksync/libraries/Diamond.sol:126`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L126)
[`ethereum/contracts/zksync/libraries/Diamond.sol:150`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L150)
[`ethereum/contracts/zksync/libraries/Diamond.sol:170`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L170)
[`ethereum/contracts/common/L2ContractHelper.sol:67`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L67)

## Use assembly to check for address(0)  
Saves 6 gas per instance if using assembly to check for address(0)  
e.g.  
  
```  
assembly {  
 if iszero(_addr) {  
  mstore(0x00, "zero address")  
  revert(0x00, 0x20)  
 }  
}  
```  
  
Instances include:
[`ethereum/contracts/zksync/DiamondProxy.sol:29`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L29)
[`ethereum/contracts/zksync/libraries/Diamond.sol:126`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L126)
[`ethereum/contracts/zksync/libraries/Diamond.sol:135`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L135)
[`ethereum/contracts/zksync/libraries/Diamond.sol:150`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L150)
[`ethereum/contracts/zksync/libraries/Diamond.sol:156`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L156)
[`ethereum/contracts/zksync/libraries/Diamond.sol:170`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L170)
[`ethereum/contracts/zksync/libraries/Diamond.sol:176`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L176)
[`ethereum/contracts/common/AllowList.sol:33`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L33)
[`zksync/contracts/bridge/L2ERC20Bridge.sol:95`](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L95)
[`zksync/contracts/bridge/L2StandardERC20.sol:44`](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L44)      
[`zksync/contracts/bridge/L2StandardERC20.sol:45`](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L45) 

## Write direct outcome, instead of performing mathematical operations   
Instances include:  
[`ethereum/contracts/zksync/Config.sol:23`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L23)
[`ethereum/contracts/zksync/Config.sol:26`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L26)

## REPLACE `MODIFIER` WITH `FUNCTION`
Modifiers make code more elegant, but cost more than normal functions.

Instances include:
[`ethereum/contracts/zksync/facets/Base.sol:15`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Base.sol#L15)
[`ethereum/contracts/zksync/facets/Base.sol:21`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Base.sol#L21)        
[`ethereum/contracts/common/AllowList.sol:33`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L33)
[`ethereum/contracts/common/AllowListed.sol:9`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowListed.sol#L9)
[`ethereum/contracts/common/ReentrancyGuard.sol:38`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/ReentrancyGuard.sol#L38)
[`ethereum/contracts/common/ReentrancyGuard.sol:65`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/ReentrancyGuard.sol#L65)
[`zksync/contracts/bridge/L2StandardERC20.sol:95`](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L95)  

## MULTIPLE ACCESSES OF A MAPPING/ARRAY SHOULD USE A LOCAL VARIABLE CACHE
instances:
[`ethereum/contracts/zksync/facets/DiamondCut.sol:105-L122`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L105-L122)
[`ethereum/contracts/zksync/libraries/Diamond.sol:95-L98`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L95-L98)

## Use calldata instead of memory for function parameters  
It is generally cheaper to load variables directly from calldata, rather than copying them to memory. Only use memory if the variable needs to be modified.  
  
Instances include: 
[`ethereum/contracts/zksync/libraries/PriorityQueue.sol:54`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L54)
