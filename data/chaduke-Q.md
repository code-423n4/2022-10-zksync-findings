
1. The data structures for DiamondStorage, FacetToSelectors, SelectorToFacet can be combined into one by eliminating many indirect access. This is not only improve the readability of the code, but also save gas due to eliminating some keccack hashing calculation of struct addresses.  The DiamondStorage struct will be the following:
```


``` 