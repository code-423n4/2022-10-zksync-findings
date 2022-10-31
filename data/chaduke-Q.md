1. Since all selectors in a facet will have the same freezability, it will be much efficient and gas-saving to store the freezability of facet at the facet level instead of at the selector level. Current implementation at the selector level has the following limitations: 
    a. Cannot get the freezability of a facet directly, has to get it through selector (see the current implementation of function *isFacetFreezable*: https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L133
 
   b. (insert anomaly) If there is no selector, a facet cannot be added.

   c. (delete anomaly) When the last selector of a facet is deleted, the facet needs to be deleted as well. 

   d. (update anomaly), if one needs to change the freezability of a facet, we need to do a loop to go though all the selector list to change all the freezalibilty of ALL selector, a gas-inefficient process. Meanwhile , if we need to replace a selector for an one-selector facet, we first need to delete the selector as well as the facet, and then add the facet back and add a new function, a very inefficient process as well. 

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

2.  https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/DiamondCut.sol#L22
in proposeDiamondCut(), one needs to make sure that the new proposal is indeed new and different from the exiting current proposal. This will avoid wasting the time of security coucil members to approve another SAME proposal again. The governor accidently propose a new but actually the same proposal. The following revision will fix this:

```
    function proposeDiamondCut(Diamond.FacetCut[] calldata _facetCuts, address _initAddress) external onlyGovernor {
        require(s.diamondCutStorage.proposedDiamondCutTimestamp == 0, "a3"); // proposal already exists

        // NOTE: governor commits only to the `facetCuts` and `initAddress`, but not to the calldata on `initAddress` call.
        // That means the governor can call `initAddress` with ANY calldata while executing the upgrade.
        bytes32 _newdiamondCutHash = keccak256(abi.encode(_facetCuts, _initAddress));
        require(_newdiamondCutHash != s.diamondCutStorage.proposedDiamondCutHash, "The proposal is not new.");
      
        s.diamondCutStorage.proposedDiamondCutHash = _newdiamondCutHash;
        s.diamondCutStorage.proposedDiamondCutTimestamp = block.timestamp;
        s.diamondCutStorage.currentProposalId += 1;

        emit DiamondCutProposal(_facetCuts, _initAddress);
    }
```

3. Might be a good idea to change name "DiamondCutFacet* to *DiaomndCutPropsalFacet* to reflect what the contract supports since most fuctions in the contract also has the word "Proposal" in it. 


4. https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/DiamondCut.sol#L22
change
```
 function proposeDiamondCut(Diamond.FacetCut[] calldata _facetCuts, address _initAddress) external onlyGovernor {
```
to
```
function proposeDiamondCut(Diamond.DiamondCutData calldata _diamondCut) external onlyGovernor {
```
No need for argument _initAddress as it is already part of DiamondCutData. In this way, the function signature will also be 
consistent with the following function:
```
function executeDiamondCutProposal(Diamond.DiamondCutData calldata _diamondCut) external onlyGovernor {
```

5. This project implements the transparent proxy pattern. However, the initialize-only-once semantics is not ensured by the current implementation. As a matter, the governor can propose diamoncut multiples times and execute the initialize function of each facet multiple times as well. There is no check that a facet has already been initialized earlier. According to OpenZeppelin,
"Since proxied contracts do not make use of a constructor, it's common to move constructor logic to an external initializer function, usually called `initialize`. It then becomes necessary to protect this initializer function so it can only be called once.". 
It is advised that the implementation should follow OpenZeppeline on this aspect, see below for details: 
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/proxy/utils/Initializable.sol
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/proxy/utils/UUPSUpgradeable.sol
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/proxy/Proxy.sol
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/proxy/transparent/TransparentUpgradeableProxy.sol


