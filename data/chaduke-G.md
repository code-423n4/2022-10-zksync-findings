
G1. https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L211-L215
We need to make sure all selectors in the same facet have the same freezablity, so we can store the *freezablity* at the facet level to save gas. In particular, we can move *bool isFreezable* from struct SelectorToFacet to struct FacetToSelectors. Then the following simpler consistency check can be used to replace L211-L215, which saves much gas:
```
require(_isSelectorFreezable == ds.facetToSelectors[_facet].isFreezable, "J1");



```

G2. https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L89
change
```
 function diamondCut(DiamondCutData memory _diamondCut) internal {
```
to
```
 function diamondCut(DiamondCutData calldata _diamondCut) internal {
```

G3. change the argument from *bytes4[] memory _selectors* to *bytes4[] calldata _selectors* for the following functions

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L119-L123

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L143-L147

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L167

G4 Cache ds.selectorToFacet as it accessed multiple times in a loop for the following functions: 
 https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L119-L123

 https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L143

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L167

G5. https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L227
cache ds.facetToSelectors as it is accessed twice

G6 https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L277
change *bytes memory _calldata* to *bytes calldata _calldata*

G7 https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/DiamondCut.sol#L59
Move L59 to the first line, since we need to make sure the governor is executing the right proposal, so this check will save gas (short-circuit rule).

G8  https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/DiamondCut.sol#L112
Move L112 to the first line, since we need to make sure the CouncilMember is approving the right proposal, so earlier check of this will save gas (short-circuit rule).

G9 _initializeDiamondCut() is called ONLY inside diamondCut() but will do NOTHING when 
initAddress == address(0) && initCalldata.length = 0; so it might be a good idea to check this condition in the caller
rather than in the callee to save gas

change line 113 to:
```
if (_init == address(0)) {
            require(_calldata.length == 0, "H"); 
} 
else    {
           require(_calldata.length != 0, "I"); 
           _initializeDiamondCut(initAddress, initCalldata);
}

```
G10:  
https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L135
Move line 135 inside function _addOneFunction so that we don't have to call getDiamondStorage() twice (_addFunctions and _addOneFunction). See the following. Similar changes can be made to _replaceFunctions and _removeFunctions. 

```
 function _addFunctions(
        address _facet,
        bytes4[] memory _selectors,
        bool _isFacetFreezable
    ) private {
        require(_facet != address(0), "G"); // facet with zero address cannot be added

        // Add facet to the list of facets if the facet address is new one
        _saveFacetIfNew(_facet);

        uint256 selectorsLength = _selectors.length;
        for (uint256 ; i < selectorsLength; unchecked{++i}) {
            _addOneFunction(_facet, _selectors[i], _isFacetFreezable);
        }
    }

 function _addOneFunction(
        address _facet,
        bytes4 _selector,
        bool _isSelectorFreezable
    ) private {
        DiamondStorage storage ds = getDiamondStorage();
        SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
        require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists

        uint16 selectorPosition = uint16(ds.facetToSelectors[_facet].selectors.length);

        // if selectorPosition is nonzero, it means it is not a new facet
        // so the freezability of the first selector must be matched to _isSelectorFreezable
        // so all the selectors in a facet will have the same freezability
        if (selectorPosition != 0) {
            bytes4 selector0 = ds.facetToSelectors[_facet].selectors[0];
            require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");
        }

        ds.selectorToFacet[_selector] = SelectorToFacet({
            facetAddress: _facet,
            selectorPosition: selectorPosition,
            isFreezable: _isSelectorFreezable
        });
        ds.facetToSelectors[_facet].selectors.push(_selector);
    }

```


G11 https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/DiamondProxy.sol#L19-L54
The following simplification to the fallback function of the DiamondProxy can greatly save gas as this is the gate of the whole system. If msg.data[:4] does not have 4 bytes for the selector, it will revert. 
```
   fallback() external payable {
        Diamond.DiamondStorage storage diamondStorage = Diamond.getDiamondStorage();
        Diamond.SelectorToFacet memory facet = diamondStorage.selectorToFacet[msg.data[:4]];
        address facetAddress = facet.facetAddress;

        require(facetAddress != address(0), "F"); // Proxy has no facet for this selector
        require(!diamondStorage.isFrozen || !facet.isFreezable, "q1"); // Facet is frozen

        assembly {
            // The pointer to the free memory slot
            let ptr := mload(0x40)
            // Copy function signature and arguments from calldata at zero position into memory at pointer position
            calldatacopy(ptr, 0, calldatasize())
            // Delegatecall method of the implementation contract returns 0 on error
            let result := delegatecall(gas(), facetAddress, ptr, calldatasize(), 0, 0)
            // Get the size of the last return data
            let size := returndatasize()
            // Copy the size length of bytes from return data at zero position to pointer position
            returndatacopy(ptr, 0, size)
            // Depending on the result value
            switch result
            case 0 {
                // End execution and revert state changes
                revert(ptr, size)
            }
            default {
                // Return data with length of size at pointers position
                return(ptr, size)
            }
        }
    }
```



