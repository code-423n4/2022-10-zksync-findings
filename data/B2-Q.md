## Missing checks for `address(0x0)` when assigning values to `address` state variables

Missing checks for zero-addresses may lead to infunctional protocol, if the variable addresses are updated incorrectly.

```
        l1Bridge = _l1Bridge;
```

>File: contracts/bridge/L2ETHBridge.sol [(line 31)](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L31)


```
       l1Bridge = _l1Bridge;
```

>File: contracts/bridge/L2ERC20Bridge.sol [(line 36)](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L36)



## Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions

While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

```
	contract L1ERC20Bridge is IL1Bridge, AllowListed, ReentrancyGuard {
```
>File: contracts/bridge/L1ERC20Bridge.sol [(line 21)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L21)


```
	contract L1EthBridge is IL1Bridge, AllowListed, ReentrancyGuard {
```
>File: contracts/bridge/L1EthBridge.sol#L17 [(line 17)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L17)

```
	Other instances of this issue are:
```
* https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L11
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L16
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L11
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Getters.sol#L13
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L16
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L12
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Base.sol#L11


## Use of `assert()` instead of `require()`

Contracts use `assert()` instead of `require()` in multiple places. This causes a Panic error on failure and prevents the use of error strings.

Prior to solc 0.8.0, assert() used the invalid opcode which used up all the remaining gas while require() used the revert opcode which refunded the gas and therefore the importance of using require() instead of assert() was greater. However, after 0.8.0, assert() uses revert opcode just like require() but creates a Panic(uint256) error instead of Error(string) created by require(). 

```
        assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);
```
>File: contracts/zksync/facets/DiamondCut.sol [(line 16)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L16)



## `Cross-Chain Replay` attack

Storing the `block.chainid` is not safe. 

```
        require(_chainId == block.chainid, "pr");
```
* File: /contracts/zksync/DiamondProxy.sol [(line 13)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L13)


## open `TODO` comments

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.

```
	 // TODO: change constant to the real root hash of empty Merkle tree (SMA-184)
```
* File: contracts/zksync/Config.sol [(line 28)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L28)
```
       // TODO: estimate gas for L1 execute
```
* File: contracts/zksync/facets/Mailbox.sol [(line 94)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L94)
```
       // TODO: Restore after stable priority op fee modeling. (SMA-1230)
```
* File: contracts/zksync/facets/Mailbox.sol [(line 127)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L127)
```
                layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)
```
* File: contracts/zksync/facets/Mailbox.sol [(line 169)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L169)


## TYPOS
```
    ///@audit: dellegate 
    /// @param initAddress The address that's dellegate called after setting up new facet changes
```
* File: contracts/zksync/libraries/Diamond.sol [(line 63)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L63)

```
    ///@audit: Cotract 
    /// @title Diamond Proxy Cotract (EIP-2535)
```
* File: contracts/zksync/DiamondProxy.sol [(line 7)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L7)
```
    ///@audit: refference 
    /// @dev It is standard implementation of ERC20 Bridge that can be used as a refference
```
* File: contracts/bridge/L1ERC20Bridge.sol [(line 19)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L19)
```
    ///@audit: exect 
    ///audit: initiailize 
    // We are expecting to see the exect two bytecodes that are needed to initiailize the bridge
```
* File: contracts/bridge/L1ERC20Bridge.sol [(line 76)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L76)


## Event is missing `indexed` fields
Each event should use three indexed fields if there are three or more fields.

```
    event DiamondCut(FacetCut[] facetCuts, address initAddress, bytes initCalldata);
```
* File: /contracts/zksync/libraries/Diamond.sol [(line 16)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L16)

```
    event BlocksRevert(uint256 totalBlocksCommitted, uint256 totalBlocksVerified, uint256 totalBlocksExecuted);
```
* File: contracts/zksync/interfaces/IExecutor.sol [(line 85)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L85)

```
    event NewPriorityRequest(
        uint256 txId,
        bytes32 txHash,
        uint64 expirationBlock,
        L2CanonicalTransaction transaction,
        bytes[] factoryDeps
```
* File: contracts/zksync/interfaces/IMailbox.sol [(line 95-100)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IMailbox.sol#L95-L100)


## Use of `block.timestamp`

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers, locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
        s.diamondCutStorage.proposedDiamondCutTimestamp = block.timestamp;
```
>File: contracts/zksync/facets/DiamondCut.sol#L28 [(line 28)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L28)

```
        bool upgradeNoticePeriodPassed = block.timestamp >=
```
>File: contracts/zksync/facets/DiamondCut.sol [(line 52)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L52)

```
        s.diamondCutStorage.lastDiamondFreezeTimestamp = block.timestamp;
```
>File: contracts/zksync/facets/DiamondCut.sol [(line 85)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L85)

```
            bool timestampNotTooSmall = block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= l2BlockTimestamp;
```
>File: contracts/zksync/facets/Executor.sol [(line 50)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L50)

```
            bool timestampNotTooBig = l2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA;
```
>File: contracts/zksync/facets/Executor.sol [(line 51)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L51)


## `Require` /`revert` should have descriptive reason strings 

```
        require(l2BlockTimestamp == _newBlock.timestamp);
```
>File: contracts/zksync/facets/Executor.sol [(line 45)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L45)
```
                revert(ptr, size)
```
>File: contracts/zksync/DiamondProxy.sol [(line 47)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L47)

```
	Other instances of the issue are:
```
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L43
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L297
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L221
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L224
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L238
* https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L50
* https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L116
* https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L122
* https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L128


## Set `garbage` value in `mapping` for deleting that

If there is a mapping data structure present inside struct, then deleting the struct doesn't delete the mapping. Instead one should use lock to lock that data structure from further use.


```
        delete depositAmount[_depositSender][_l1Token][_l2TxHash];
```
>File: contracts/bridge/L1ERC20Bridge.sol [(line 212)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L212)

```
        delete depositAmount[_depositSender][_l2TxHash];
```
>File: contracts/bridge/L1EthBridge.sol [(line 169)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L169)


## `NatSpec` is incomplete
```
    /// @audit Missing: '@param'
    /// @dev Add new functions to the diamond proxy
    /// NOTE: expect but NOT enforce that `_selectors` is NON-EMPTY array
    function _addFunctions(
```
* File: contracts/zksync/libraries/Diamond.sol [(line 117-119)]( 

```
    /// @audit Missing: '@param'
    /// @dev Change associated facets to already known function selectors
    /// NOTE: expect but NOT enforce that `_selectors` is NON-EMPTY array
    function _replaceFunctions(
```
* File: contracts/zksync/libraries/Diamond.sol [(line 141-143)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L141-L143)
```
	Other instances of the issue are:
```
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L225-L227 /// @audit Missing: '@param'	
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L275-L277 /// @audit Missing: '@param'	
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L88-L89  /// @audit Missing: '@param'& '@return'
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L176-L177  /// @audit Missing: '@param'& '@return'
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L273-L279  /// @audit Missing: '@param'& '@return'
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L348_L349  /// @audit Missing: '@param'& '@return'
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Getters.sol#L73_L86  /// @audit Missing: '@param'	
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Getters.sol#L115-L122 /// @audit Missing: '@param'	
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Getters.sol#L132-L133  /// @audit Missing: '@param'	
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Getters.sol#L145-L146  /// @audit Missing: '@param'	
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Getters.sol#L171-L172  /// @audit Missing: '@param'	
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Getters.sol#L183-L184  /// @audit Missing: '@param'	
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L35-L46  /// @audit Missing: '@return'	
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L49-L55  /// @audit Missing: '@param'& '@return'
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L116-L123  /// @audit Missing: '@param'& '@return'
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L143-L153  /// @audit Missing: '@param'& '@return'
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L215-L219  /// @audit Missing: '@param'& '@return'
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L134-L140 /// @audit Missing: '@param'	
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L148-L154  /// @audit Missing: '@param'& '@return'
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L163-L164  /// @audit Missing: '@param'& '@return'
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L259-L266  /// @audit Missing: '@param'& '@return'

## Missing event and timelock for critical parameter change

Events help non-contract tools to track changes, and events prevent users from being surprised by changes.

```
    ) external onlyOwner {
```
>File: contracts/common/AllowList.sol [(line 94)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L94)


```
    ) external onlyOwner { 
```
>File: /contracts/common/AllowList.sol [(line 118)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L118)

```
	Other instances of the issue are:
```

* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L140
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L57
* https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L65