
## N.1 There is no good way to update a facet's `isFreezable` except call `_removeFunctions` then `_addFunctions` back

## Impact
It is expensive to call `_removeFunctions` and `_addFunctions`, as it involves a lot of operations. 

## Proof of Concept
As the protocol becomes more mature, it is natural to freeze specific facets. A function can be created to specifically switch `isFreezable` of all selectors of a given facet.

## Tools Used
VS Code

## Recommended Mitigation Steps
in Diamond library add:
```
function _changeFreezability(address _facet, bool _isFreezable) {
    DiamondStorage storage ds = getDiamondStorage();
    uint256 selectorLen = ds.facetToSelectors[_facet].selectors.length;
    for (init i; i<selectLen; i=i.uncheckedInc()) {
        bytes4 selector = ds.facetToSelectors[_facet].selectors[i];
        ds.selectorToFacet[selector].isFreezable = _isFreezable;
    }
}
```

---

## N.2 Fix inaccurate comments

## Impact
It is confusing when comments are inconsistent with the code, even when the code itself is correct

## Proof of Concept
In [`require(approvedBySecurityCouncil || upgradeNoticePeriodPassed, "a6"); // notice period should expire`](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/DiamondCut.sol#L55)
The code says governor can execute a proposal immediately as long as enough approvals have been gathered. Yet the comment says `notice period should expire.` 

Another example, where comments about `_l1Token` in [`L2ERC20Bridge`](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ERC20Bridge.sol#L47) was copied from `L2EthBridge`, thus inaccurate.

## Tools Used
VS Code 

## Recommended Mitigation Steps
fix the comment to match the code

---

## N.3 Fix inaccurate doc

## Impact
It is confusing when docs are inconsistent with the code, even when the code itself is correct

## Proof of Concept
According to the doc "Several (of none) logs from the L2_TO_L1_MESSENGER with the **key** == hashedMessage where hashedMessage is a hash of an arbitrary-length message that is sent from L2", however, in the code: 
[`(bytes32 hashedMessage, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + 56);`](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L116). For L2_TO_L1_MESSENGER the key is bytes32(msg.sender) at 24 and value is hashedMessage at 56. So, the code is correct, but the document should be edited.

## Tools Used
VS Code 

## Recommended Mitigation Steps
replace **key** with **value**.

----

## N.4 Checking `require(emittedL2Logs.length % L2_TO_L1_LOG_SERIALIZE_SIZE == 0);`

## Impact


## Proof of Concept
[`_processL2Logs`](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L111) does extensive checking of l2logs to make sure everything lines up.
As a sanity check, the new requirement makes it more explicit about the structure of l2logs.

## Tools Used
VS Code 

## Recommended Mitigation Steps
add `require(emittedL2Logs.length % L2_TO_L1_LOG_SERIALIZE_SIZE == 0);` before [Executor.sol#L111](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L111)


## N.5 Align doc with code

## Proof of Concept
The doc specifies `_processL2Logs` processes `Several (or none) logs from other addresses with arbitrary parameters.`. However the code does not show any trace of such logic. 

After communicating with the dev team, it is apparent this paragraph is meant for the next phase of the project, thus should be removed.

---

## N.6 Remove `_calculateBlockHash(...)` from Executor
[`_calculateBlockHash`](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L80) is an internal function in `Executor`, and it is not called in the codebase. Can be removed.

---
## N.7 Missing setter function to set an account as `securityCouncilMembers`

## Impact
A new facet needs to be reployed to add members to `securityCouncilMembers`

## Proof of Concept
As the code stands now, there does not seem to be a setter to set an account as `securityCouncilMembers`. The emergency approval route is not really needed either since `UPGRADE_NOTICE_PERIOD` is currently set as 0. However, as these logics are mobilized, a setter will need to be reployed.

----

## N.8 `isFacetFreezable` and `isFunctionFreezable` behaves differently for non-existing values
[`isFacetFreezable`](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L133) returns false for non-exising `_facet`.
[`isFunctionFreezable`](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L146) reverts for non-existing `_selector`.
The the sake of consistency, they should exhibit similar behavior.

---

## N.9 `setVerifierParams` does not check the equivalence of old and new paramters before setting values
All other functions in `Governance.sol` checkes the equivalence of old and new values before changing the exisitng value. [`setVerifierParams`](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Governance.sol#L104) is the only exception. It makes sense to check new check first as well in `setVerifierParams`.
