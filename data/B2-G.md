## Use `calldata` instead of `memory`

If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.

Try to use calldata as a data location because it will avoid copies and also makes sure that the data cannot be modified.

```
        L2Log memory _log,
```
>File: contracts/zksync/facets/Mailbox.sol [(line 43)]( https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L43)
```
        L2Log memory _log,
```
>File: contracts/zksync/facets/Mailbox.sol [(line 53)]( https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L53)

```
	Other instances of the issue are:
```

* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IMailbox.sol#L61
* https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/ExternalDecoder.sol#L10
* https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/ExternalDecoder.sol#L15
* https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/L2ContractHelper.sol#L6
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L6
* https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L43


## call `emit` from `storage` is more expensive

Emmitting an event using storage data is more expensive

```
        emit DiamondCut(facetCuts, initAddress, initCalldata);
```
>File: contracts/zksync/libraries/Diamond.sol [(line 114)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L114)

```
        emit WithdrawalFinalized(l1Receiver, CONVENTIONAL_ETH_ADDRESS, amount);
```
>File: contracts/bridge/L1EthBridge.sol [(line 210)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L210)

##  Multiple `key` mappings can be combined into a single mapping of `key` to a struct, where appropriate.

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot

```
    mapping(address => bool) public isAccessPublic;

    /// @notice The mapping that stores permissions to call the function on the target address by the caller
    /// @dev caller => target => function signature => permission to call target function for the given caller address
    mapping(address => mapping(address => mapping(bytes4 => bool))) public hasSpecialAccessToCall;
```
>File: contracts/common/AllowList.sol [(line 26-30)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L26-L30)

```
    mapping(address => bool) securityCouncilMembers;
    mapping(address => uint256) securityCouncilMemberLastApprovedProposalId;
```
>File: contracts/zksync/Storage.sol [(line 22-23)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L22-L23)

```
    mapping(uint256 => bytes32) storedBlockHashes;
    /// @dev Stored root hashes of L2 -> L1 logs
    mapping(uint256 => bytes32) l2LogsRootHashes;
```
>File: /contracts/zksync/Storage.sol [(line 87-89)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L87-L89)


## Splitting `require()` statements that use `&&` saves gas (even a single &&)

Require statements including conditions with the && operator can be broken down in multiple require statements to save gas.



```
        require(
            callersLength == _targets.length &&
                callersLength == _functionSigs.length &&
                callersLength == _enables.length,
```
>File: contracts/common/AllowList.sol [(line 96-99)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L96-L99)

```
        require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
```
>File: contracts/common/L2ContractHelper.sol [(line 65)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L65)


##  Don’t use require functions in loop . It is better to break than to revert 


```
        for (uint256 i = 0; i < facetCutsLength; ++i) {
            Action action = facetCuts[i].action;
            address facet = facetCuts[i].facet;
            bool isFacetFreezable = facetCuts[i].isFreezable;
            bytes4[] memory selectors = facetCuts[i].selectors;

            require(selectors.length > 0, "B"); // no functions for diamond cut
```
>File: contracts/zksync/libraries/Diamond.sol [(line 94-100)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L94-L100)

```
        for (uint256 i = 0; i < selectorsLength; ++i) {
            bytes4 selector = _selectors[i];
            SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
            require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists
```
>File: contracts/zksync/libraries/Diamond.sol [(line 132-135)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L132-L135)

```
	Other instances of the issue are:
```
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L153-L156
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L173-L176
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L240-L241

## `internal` functions only called once can be `inlined` to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.


```
    function _maxU256(uint256 a, uint256 b) internal pure returns (uint256) {
```
>File: contracts/zksync/facets/Executor.sol [(line 349)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L349)

```
	Other instances of the issue are:
```
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L354
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L362
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L372
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L376
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L75
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L116
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L144
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L216
* https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L104-L108
* https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L73

## <x> += <y> costs more gas than <x> = <x> + <y> for state variables

```
        s.diamondCutStorage.currentProposalId += 1;
```
>File: contracts/zksync/facets/DiamondCut.sol [(line 29)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L29) 

## Ununsed named `return`

Using both named returns and a return statement isn’t necessary. Removing one of those can improve code clarity:


```
        returns (StoredBlockInfo memory storedNewBlock)
```
>File: contracts/zksync/facets/Executor.sol#L26 [(line 26)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L26) 


## Tight Variable Packing

Solidity contracts have contiguous 32 bytes (256 bits) slots used in storage. By arranging the variables, it is possible to minimize the number of slots used within a contract’s storage and therefore reduce deployment costs.

address type variables are each of 20 bytes size (way less than 32 bytes). However, they here take up a whole 32 bytes slot (they are contiguous).

As bool type variables are of size 1 byte, there’s a slot here that can get saved by moving them closer to an address

* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L69-L105
* https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L29
