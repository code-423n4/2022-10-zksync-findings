### 1. Don't use `x += 1` with state variable because it consumes more gas than `x = x + 1`.

Contract: DiamondCutFacet

Method: proposeDiamondCut

PoC: run `yarn test --grep "Diamond proxy tests"` before and after applying the patch.

```diff
diff --git a/ethereum/contracts/zksync/facets/DiamondCut.sol b/ethereum/contracts/zksync/facets/DiamondCut.sol
index 9a60aea..ee0e2b0 100644
--- a/ethereum/contracts/zksync/facets/DiamondCut.sol
+++ b/ethereum/contracts/zksync/facets/DiamondCut.sol
@@ -26,7 +26,7 @@ contract DiamondCutFacet is Base, IDiamondCut {
         // That means the governor can call `initAddress` with ANY calldata while executing the upgrade.
         s.diamondCutStorage.proposedDiamondCutHash = keccak256(abi.encode(_facetCuts, _initAddress));
         s.diamondCutStorage.proposedDiamondCutTimestamp = block.timestamp;
-        s.diamondCutStorage.currentProposalId += 1;
+        s.diamondCutStorage.currentProposalId = s.diamondCutStorage.currentProposalId + 1;

         emit DiamondCutProposal(_facetCuts, _initAddress);
     }
```

|Gas before|Gas after|Gas saved|
|-|-|-|
|107374 |107340|34|

---

### 2. Combined require statement is less efficient than separate ones

Contract: AllowList

Method: setBatchPermissionToCall

PoC: run `yarn test --grep "Allow list tests"` before and after applying the patch.

Proposed mitigation:

```diff
diff --git a/ethereum/contracts/common/AllowList.sol b/ethereum/contracts/common/AllowList.sol
index c6c0e14..03e5b53 100644
--- a/ethereum/contracts/common/AllowList.sol
+++ b/ethereum/contracts/common/AllowList.sol
@@ -93,12 +93,11 @@ contract AllowList is IAllowList {
         bool[] calldata _enables
     ) external onlyOwner {
         uint256 callersLength = _callers.length;
-        require(
-            callersLength == _targets.length &&
-                callersLength == _functionSigs.length &&
-                callersLength == _enables.length,
-            "yw"
-        ); // The size of arrays should be equal
+
+        // The size of arrays should be equal
+        require(callersLength == _targets.length, "yw");
+        require(callersLength == _functionSigs.length, "yw");
+        require(callersLength == _enables.length, "yw");

         for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
             _setPermissionToCall(_callers[i], _targets[i], _functionSigs[i], _enables[i]);
```

|Gas before|Gas after|Gas saved|
|-|-|-|
|55071|55055|16|
