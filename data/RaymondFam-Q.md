Report contents changed: ## Require Error Message
Consider making the require error message more verbose and descriptive so all other peer developers would better be able to comprehend the intended statement logic. Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L13
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L24
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L29-L30

## Expression for Calculated Values
Long bytes of literal values assigned to constants may incur typo/human error. Considering assigning these constants with their corresponding expression. For instance, the following two instances could have their code lines correspondingly refactored to:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L10-L14

```
    bytes32 constant DIAMOND_INIT_SUCCESS_RETURN_VALUE = keccak256("diamond.zksync.init") - 1;
        
    bytes32 constant DIAMOND_STORAGE_POSITION = keccak256("diamond.standard.diamond.storage") - 1; 

```
Note: The calculated byte values may however be included as a comment instead.

## Lines Too Long
Lines in source code are typically limited to 80 characters, but it’s reasonable to stretch beyond this limit when need be as monitor screens theses days are comparatively larger. Considering the files will most likely reside in GitHub that will have a scroll bar automatically kick in when the length is over 164 characters, all code lines and comments should be split when/before hitting this length. Keep line width to max 120 characters for better readability where possible. Here is one of the instances entailed:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L14

## Function Calls in Loop Could Lead to Denial of Service
Function calls made in unbounded loop are error-prone with potential resource exhaustion as it can trap the contract due to the gas limitations or failed transactions. Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L94
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L132
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L153
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L173

Consider bounding the loop where possible to avoid unnecessary gas wastage and denial of service.

## Unspecific Compiler Version Pragma
For most source-units the compiler version pragma is very unspecific ^0.8.0. While this often makes sense for libraries to allow them to be included with multiple different versions of an application, it may be a security risk for the actual application implementation itself. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up actually checking a different EVM compilation that is ultimately deployed on the blockchain.

Avoid floating pragmas where possible. It is highly recommend pinning a concrete compiler version (latest without security issues) in at least the top-level “deployed” contracts to make it unambiguous which compiler version is being used. Rule of thumb: a flattened source-unit should have at least one non-floating concrete solidity compiler version pragma.

## Sanity Checks
Zero address and zero value checks should be implemented on `initialize()` of `DiamondInit.sol`  to avoid accidental error(s) that could result in non-functional calls associated with it. This is of particular concern considering a `reentrancyGuardInitializer` has been in place preventing the function from getting re-initialized.

## Un-indexed Parameters in Events
Consider indexing parameters for events, serving as logs filter when looking for specifically wanted data. Up to three parameters in an event function can receive the attribute `indexed` which will cause the respective arguments to be treated as log topics instead of data. There are the instances entailed:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L16