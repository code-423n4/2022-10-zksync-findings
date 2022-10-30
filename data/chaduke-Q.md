1. Since all selectors in a facet will have the same freezability, according to https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L211
It will be much better (save gas, easier to modify freezablity, and avoid insert/update/delete anomalies) to save the freezablity of selectors at the fact level instead of at the selector level. In particular, we can move  *bool isFreezable* from struct *SelectorToFacet* to *FacetToSelectors* as follows, so that the freezability of a facet is only saved once rather than multiple times an the need for check consistency in line 
```
    struct SelectorToFacet {
        address facetAddress;
        uint16 selectorPosition;
    }

    struct FacetToSelectors {
        bytes4[] selectors;
        uint16 facetPosition;
        bool isFreezable;
    }

```