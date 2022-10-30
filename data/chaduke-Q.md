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
The SaveFacetIfNew() will be changed to the following with *isFreezable* added as the new argument:
```
   function _saveFacetIfNew(address _facet, bool _isFreezable) private {
        DiamondStorage storage ds = getDiamondStorage();

        uint256 selectorsLength = ds.facetToSelectors[_facet].selectors.length;
        // If there are no selectors associated with facet then save facet as new one
        if (selectorsLength == 0) {
            ds.facetToSelectors[_facet].facetPosition = uint16(ds.facets.length);
            ds.facets.push(_facet);
            ds.facetToSelectors[_facet].isFreezable = _isFreezable;  // @audit: set the new _facet's freezability
        }
    }
```
