0. https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L211-L215
We need to make sure all selectors in the same facet have the same freezablity, so we can store the *freezablity* at the facet level to save gas. In particular, we can move *bool isFreezable* from struct SelectorToFacet to struct FacetToSelectors. Then the following simpler consistency check can be used to replace L211-L215, which saves much gas:
```
require(_isSelectorFreezable == ds.facetToSelectors[_facet].isFreezable, "J1");

```

1. https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L89
change
```
 function diamondCut(DiamondCutData memory _diamondCut) internal {
```
to
```
 function diamondCut(DiamondCutData calldata _diamondCut) internal {
```

2. change the argument from *bytes4[] memory _selectors* to *bytes4[] calldata _selectors* for the following functions

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L119-L123

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L143-L147

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L167

3. Cache ds.selectorToFacet as it accessed multiple times in a loop for the following functions: 
 https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L119-L123

 https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L143

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L167

4. https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L227
cache ds.facetToSelectors as it is accessed twice

5. https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L277
change *bytes memory _calldata* to *bytes calldata _calldata*






