QA:
1.Make a check on facetCutsLength so that it never equal to zero;

If facetCutsLength is equal to zero than further condition in for loop like here,"i < facetCutsLength"  will always fail leads to unrequired wastage of gas.Therefore implement a check 

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L93
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L132


Recommendation: Make a check require(facetCutsLength != 0,"should be != 0") and require(selectorsLength  != 0,"should be != 0")

2.First declare a variable as uint256 later declaring as uint16 can make a underflow and overflow condition to occur:

 uint256 facetPosition = ds.facetToSelectors[_facet].facetPosition;
 https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L260

 uint256 selectorPosition = ds.selectorToFacet[_selector].selectorPosition;
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L231

In Diamond._removeFacet and Diamond._removeOneFunction first declaring variable as uint256(uint256 facetPosition = ds.facetToSelectors[_facet].facetPosition and uint256 selectorPosition = ds.selectorToFacet[_selector].selectorPosition) than after moving it to the facet position making it as uint16 will raise a condition of underflow and underflow.
            
            ds.facetToSelectors[lastFacet].facetPosition = uint16(facetPosition);
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L268

            ds.selectorToFacet[lastSelector].selectorPosition = uint16(selectorPosition);
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L239

Recommendation: Casting facetPosition and selectorPosition as uint256 throughout the function,whenever using it in a function.

3.Make a address check on address _facet

In Diamond._saveFacetIfNew before Adding address to the list of known facets address check is required to conform the address which is been adding should not be equal to zero.

    function _saveFacetIfNew(address _facet) private {
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L184

Make a check condition as other functions did: require(_facet == address(0), "a1");

Issue:Non-critical
1.Wrong comment written:

In Diamond._removeFunctions in require statement it is stated as 
require(_facet == address(0), "a1"); and in comment it is written as // facet address must be zero

Make // facet address must be non-zero address