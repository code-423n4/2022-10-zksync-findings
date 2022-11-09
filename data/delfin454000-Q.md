## QA Report - low risk

### Missing checks for address(0x0) when assigning values to address state variables
___
[L1ERC20Bridge.sol: L79](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L79)
```solidity
        l2TokenFactory = _l2TokenFactory;
```
___
[L2ERC20Bridge.sol: L36](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ERC20Bridge.sol#L36)
```solidity
        l1Bridge = _l1Bridge;
```
___
[L2ETHBridge.sol: L31](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ETHBridge.sol#L31)
```solidity
        l1Bridge = _l1Bridge;
```
___
___


###  Outdated Open Zeppelin versions are used

Outdated Open Zeppelin version are used. See [ethereum/package.json](https://github.com/code-423n4/2022-10-zksync/blob/358f38736351a8a27e325dfcb665eeba5ec02bd5/ethereum/package.json) and [zksync/package.json](https://github.com/code-423n4/2022-10-zksync/blob/358f38736351a8a27e325dfcb665eeba5ec02bd5/zksync/package.json)

The versions used have known vulnerabilties, including three with high severity. See [Security Advisories](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories). 
___
___


### Duplicate contract names
 
The contract name `L2ContractHelper.sol` is used twice in the contracts in scope, as shown below. 

The existence of two contracts with the same name could cause confusion, as well as compilation problems under some circumstances.  

[zksync/contracts/L2ContractHelper.sol](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/L2ContractHelper.sol)

[ethereum/contracts/common/L2ContractHelper.sol](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/common/L2ContractHelper.sol) 

Similarly for `IL1Bridge.sol` and `IL2Bridge.sol`
___
___


### `Solidity pragma` version should be upgraded to latest available version

The current solidity version in contracts is `^0.8.0`, compared to the latest version of `^0.8.17`. Only the latest version receives security fixes.

Also, five contracts use `pragma solidity ^0.8;`, which I assume are typos and have included under __Typos__

___
___


## QA Report: non-critical

### Unclear comments 
While a comment may contain abbreviations, shorthand for well-known or previously defined terms, inconsistent or nonstandard punctuation, and use of minor grammatical errors, it should be readable. 
In other words, it should communicate clearly, immediately and without ambiguity. The comments below should be improved:
___
[DiamondCut.sol: L45](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/DiamondCut.sol#L45)
```solidity
    /// `initCalldata` can be arbitrarily.
```
___
[Diamond.sol: L285-286](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L285-L286)
```solidity
            // Check that called contract returns magic value to make sure that contract logic
            // supposed to be used as diamond cut initializer.
```
___
[L1EthBridge.sol: L242](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1EthBridge.sol#L242)
```solidity
    /// @dev Ignore function input and always return zero address as a only one token that the bridge process
```
___
___

### Long single-line comments 
In theory, comments over 80 characters should wrap using multi-line syntax. 

Even if somewhat longer lines (up to 120 characters) are acceptable and a scroll bar is provided, there are cases where long comments interfere with readability. 
Below are instances of extra-long lines whose readability could be improved by wrapping. 

I've divided them into three types. First are NatSpec statements of at least 120 characters. 

Such a statement can be wrapped using a small indentation in the continuation line (to indicate that the line has wrapped), 
along with a period at its close (to indicate the end of the line), as shown below:

[IMailbox.sol: L16](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IMailbox.sol#L16)
```solidity
    /// @param maxPriorityFeePerErg The additional fee that is paid directly to the validator to incentivize them to include the transaction in a block. Analog to the EIP-1559 `maxPriorityFeePerGas` on an L1 transactions
```
Suggestion:
```solidity
    /// @param maxPriorityFeePerErg The additional fee that is paid directly to the validator to incentivize them
    ///  to include the transaction in a block. Analog to the EIP-1559 `maxPriorityFeePerGas` on an L1 transactions.
```
Similarly for the following 33 statements:

[DiamondInit: L24](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/DiamondInit.sol#L24)

[Config.sol: L8](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Config.sol#L8)

[Config.sol: L59](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Config.sol#L59)

[Storage.sol: L9](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Storage.sol#L9)

[Storage.sol: L11](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Storage.sol#L11)

[Storage.sol: L12](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Storage.sol#L12)

[Storage.sol: L15](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Storage.sol#L15)

[Storage.sol: L28](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Storage.sol#L28)

[Storage.sol: L80](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Storage.sol#L80)

[DiamondCut.sol: L104](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/DiamondCut.sol#L104)

[Getters.sol: L51](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L51)

[Mailbox.sol: L23](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L23)

[Mailbox.sol: L94](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L94)

[Mailbox.sol: L100](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L100)

[Mailbox.sol: L105](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L105)

[Mailbox.sol: L178](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L178)

[Mailbox.sol: L179](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L179)

[Diamond: L9](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L9)

[IExecutor.sol: L9](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IExecutor.sol#L9)

[IExecutor.sol: L29](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IExecutor.sol#L29)

[IExecutor.sol: L38](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IExecutor.sol#L38)

[IMailbox.sol: L9](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IMailbox.sol#L9)

[IMailbox.sol: L11](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IMailbox.sol#L11)

[IMailbox.sol: L12](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IMailbox.sol#L12)

[IMailbox.sol: L14](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IMailbox.sol#L14)

[IMailbox.sol: L15](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IMailbox.sol#L15)

[IMailbox.sol: L17](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IMailbox.sol#L17)

[AllowList.sol: L64](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/common/AllowList.sol#L64)

[AllowList.sol: L88](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/common/AllowList.sol#L88)

[UnsafeBytes.sol: L8](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/common/libraries/UnsafeBytes.sol#L8)

[L2ERC20Bridge.sol: L47](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ERC20Bridge.sol#L47)

[L2ETHBridge: L39](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ETHBridge.sol#L39)

[L2StandardERC20.sol: L17](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2StandardERC20.sol#L17)
___

The second type are comment lines over 120 characters in length. 
Like the first type, such statements can be wrapped using a small indentation in the second line (after the `//`) along with a closing period, as shown below:

[L1ERC20Bridge.sol: L196](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1ERC20Bridge.sol#L196)
```solidity
        // - l2ShardId = 0 (means that L1 -> L2 transaction was processed in a rollup shard, other shards are not available yet anyway)
```
Suggestion:
```solidity
        // - l2ShardId = 0 (means that L1 -> L2 transaction was processed in a rollup shard, 
        //   other shards are not available yet anyway).
```
Similarly for the following eight cases:

[DiamondProxy.sol: L12](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/DiamondProxy.sol#L12)

[Storage.sol: L37](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Storage.sol#L37)

[DiamondCut.sol: L25](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/DiamondCut.sol#L25)

[DiamondCut.sol: L84](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/DiamondCut.sol#L84)

[Executor.sol: L103](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L103)

[Merkle.sol: L12](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Merkle.sol#L12)

[L1EthBridge.sol: L155](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1EthBridge.sol#L155)

[L1EthBridge.sol: L220](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1EthBridge.sol#L220)
___

The third type of extra-long line includes both code and comments. 
If a line containing a comment is longer than 120 characters, it makes sense to put the comment in the line above, as shown:

[DiamondCut.sol: L106](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/DiamondCut.sol#L106)
```solidity
        require(s.diamondCutStorage.securityCouncilMembers[msg.sender], "a9"); // not a security council member
```
Suggestion:
```solidity
        // not a security council member
        require(s.diamondCutStorage.securityCouncilMembers[msg.sender], "a9"); 
```
Similarly for the following seven cases:

[DiamondCut.sol: L108](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/DiamondCut.sol#L108)

[DiamondCut.sol: L112](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/DiamondCut.sol#L112)

[Executor.sol: L159](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L159)

[Executor.sol: L199](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L199)

[Executor.sol: L216](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L216)

[Governance.sol: L30](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Governance.sol#L30)

[Diamond.sol: L156](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L156)
___
___

### Named return variable is not used
___
[Executor.sol: L23-28](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L23-L28)
```solidity
    function _commitOneBlock(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)
        internal
        view
        returns (StoredBlockInfo memory storedNewBlock)
    {
        require(_newBlock.blockNumber == _previousBlock.blockNumber + 1, "f"); // only commit next block
```
The named return variable `storedNewBlock` is never used
___
___


### TODOs and other open items
Comments that refer to open items should be addressed and more fully explained, or else modified or removed. Below are several instances:
___
[Config.sol: L28](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Config.sol#L28)
```solidity
// TODO: change constant to the real root hash of empty Merkle tree (SMA-184)
```
___
[Mailbox.sol: L94](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L94)
```solidity
        // TODO: estimate gas for L1 execute
```
___
[Mailbox.sol: L127](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L127)
```solidity
        // TODO: Restore after stable priority op fee modeling. (SMA-1230)
```
___
[Mailbox.sol: L169](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L169)
```solidity
                layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)
```
___
[IExecutor.sol: L56](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IExecutor.sol#L56)
```solidity
    /// TODO: The verifier integration is not finished yet, change the structure for compatibility later
```
___
[IMailbox.sol: L46-47](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IMailbox.sol#L46-L47)
```solidity
        // Reserved dynamic type for the future use-case. Using it should be avoided,
        // But it is still here, just in case we want to enable some additional functionality.
```
___
[L1EthBridge.sol: L24-30](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1EthBridge.sol#L24-L30)
```solidity
    /// @dev Ergs limit for requesting L2 deposit finalization transaction
    /// NOTE: constant is not calculated accurately, because ergs count in L2 is not stable yet
    uint256 constant DEPOSIT_ERGS_LIMIT = 2097152;


    /// @dev Ergs limit for requesting L1 -> L2 transaction of deploying L2 bridge instance
    /// NOTE: constant is not calculated accurately, because ergs count in L2 is not stable yet
    uint256 constant DEPLOY_L2_BRIDGE_COUNTERPART_ERGS_LIMIT = 2097152;
```
___
[UnsafeBytes.sol: L10-16](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/common/libraries/UnsafeBytes.sol#L10-L16)
```solidity
 * @dev WARNING!
 * 1) Functions don't check the length of the bytes array, so it can go out of bounds.
 * The user of the library must check for bytes length before using any functions from the library!
 *
 * 2) Read variables are not cleaned up - https://docs.soliditylang.org/en/v0.8.16/internals/variable_cleanup.html.
 * Using data in inline assembly can lead to unexpected behavior!
 */
```
___
___

### Missing NatSpec for `functions`, `events`, `structs` and `constructors`
NatSpec statements are partially or wholly missing for some `functions`, `events`, `structs` and `constructors`.  Below are some representative examples.

Note that I am not including the many functions marked `private` or `internal`, for which NatSpec is not required. 

[DiamondCut.sol: L43-46](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/DiamondCut.sol#L43-L46)
```solidity
    /// @notice Executes a proposed governor upgrade. Only the current governor can execute the upgrade.
    /// NOTE: Governor can execute diamond cut ONLY with proposed `facetCuts` and `initAddress`.
    /// `initCalldata` can be arbitrarily.
    function executeDiamondCutProposal(Diamond.DiamondCutData calldata _diamondCut) external onlyGovernor {
```
Missing: `@param _diamondCut`
___
[Mailbox.sol: L35-45](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L35-L45)
```solidity
    /// @notice Prove that a specific L2 log was sent in a specific L2 block
    /// @param _blockNumber The executed L2 block number in which the log appeared
    /// @param _index The position of the l2log in the L2 logs Merkle tree
    /// @param _log Information about the sent log
    /// @param _proof Merkle proof for inclusion of the L2 log
    function proveL2LogInclusion(
        uint256 _blockNumber,
        uint256 _index,
        L2Log memory _log,
        bytes32[] calldata _proof
    ) external view returns (bool) {
```
Missing: `@return`
___

NatSpec statements are also partially or wholly missing for the following: 

***AllowList.sol***

[L32-35](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/common/AllowList.sol#L32-L35)

***DiamondProxy.sol***

[L10-15](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/DiamondProxy.sol#L10-L15)

***Executor.sol***

[L148-153](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L148-L153), [L205-208](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L205-L208), [L219-225](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L219-L225), [L332-336](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L332-L336)

***ExternalDecoder.sol***

[L9-10](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/ExternalDecoder.sol#L9-L10), [L14-15](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/ExternalDecoder.sol#L14-L15)

***Getters.sol***

[L21-22](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L21-L22), [L26-27](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L26-L27), [L31-32](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L31-L32), [L36-37](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L36-L37), [L41-42](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L41-L42), [L46-47](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L46-L47)

[L51-52](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L51-L52), [L56-59](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L56-L59), [L63-64](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L63-L64), [L68-69](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L68-L69), [L73-74](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L73-L74), [L78-79](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L78-L79)

[L83-86](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L83-L86), [L90-91](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L90-L91), [L95-96](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L95-L96), [L100-101](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L100-L101), [L105-106](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L105-L106)

[L110-111](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L110-L111), [L115-116](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L115-L116), [L120-122](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L120-L122), [L126-127](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L126-L127), [L132-133](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L132-L133)

[L145-146](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L145-L146), [L156-157](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L156-L157), [L171-172](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L171-L172), [L177-178](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L177-L178), [L183-184](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L183-L184)

***L1ERC20Bridge.sol***

[L55-60](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1ERC20Bridge.sol#L55-L60)

***L1EthBridge.sol***

[L46-51](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1EthBridge.sol#L46-L51)

***L2ContractHelper.sol***

zksync/contracts/L2ContractHelper.sol

[L6](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/L2ContractHelper.sol#L6), [L9-14](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/L2ContractHelper.sol#L9-L14)

***L2ContractHelper.sol***

ethereum/contracts/common/L2ContractHelper.sol

[L10-15](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/common/L2ContractHelper.sol#L10-L15), [L17](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/common/L2ContractHelper.sol#L17), [L19-23](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/common/L2ContractHelper.sol#L19-L23)

***L2ERC20Bridge.sol***

[L31-35](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ERC20Bridge.sol#L31-L35), [L112-113](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ERC20Bridge.sol#L112-L113), [L30-34](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ETHBridge.sol#L30-L34), [L36-47](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ETHBridge.sol#L36-L47)

[L57-63](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ETHBridge.sol#L57-L63), [L78-79](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ETHBridge.sol#L78-L79), [L83-84](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ETHBridge.sol#L83-L85)

***L2StandardERC20.sol***

[L41-43](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2StandardERC20.sol#L41-L43), [L100-102](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2StandardERC20.sol#L100-L102), [L107-109](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2StandardERC20.sol#L107-L109), [L114](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2StandardERC20.sol#L114), [L120](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2StandardERC20.sol#L120), [L126](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2StandardERC20.sol#L126)

***Mailbox.sol***

[L87-93](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L87-L93)
___
___

### Event is missing `indexed` fields
Each `event` should use three `indexed` fields if there are three or more fields
___
[Diamond.sol: L16](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L16)
```solidity
    event DiamondCut(FacetCut[] facetCuts, address initAddress, bytes initCalldata);
```
___
[IDiamondCut.sol: L30-35](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L30-L35)
```solidity
    event EmergencyDiamondCutApproved(
        address indexed _address,
        uint256 currentProposalId,
        uint256 securityCouncilEmergencyApprovals,
        bytes32 indexed proposedDiamondCutHash
    );
```
___
[IExecutor.sol: L85](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IExecutor.sol#L85)
```solidity
    event BlocksRevert(uint256 totalBlocksCommitted, uint256 totalBlocksVerified, uint256 totalBlocksExecuted);
```
___
[IMailbox.sol: L95-101](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IMailbox.sol#L95-L101)
```solidity
    event NewPriorityRequest(
        uint256 txId,
        bytes32 txHash,
        uint64 expirationBlock,
        L2CanonicalTransaction transaction,
        bytes[] factoryDeps
    );
```
___
[IL1Bridge.sol: L11](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L11)
```solidity
    event WithdrawalFinalized(address indexed to, address indexed l1Token, uint256 amount);
```
___
[IL1Bridge.sol: L13](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L13)
```solidity
    event ClaimedFailedDeposit(address indexed to, address indexed l1Token, uint256 amount);
```
___
[L2StandardERC20.sol: L12](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2StandardERC20.sol#L12)
```solidity
    event BridgeInitialization(address indexed l1Token, string name, string symbol, uint8 decimals);
```
___
___


### Typos
___
[DiamondProxy.sol: L7](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/DiamondProxy.sol#L7)
```solidity
/// @title Diamond Proxy Cotract (EIP-2535)
```
Change `Cotract` to `Contract`
___
[Storage.sol: L36](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Storage.sol#L36)
```solidity
/// @dev The sender is an `address` type, although we are using `uint256` for addreses in `L2CanonicalTransaction`.
```
Change `addreses` to `addresses`
___
[Executor.sol: L216](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L216)
```solidity
        require(s.totalBlocksExecuted <= s.totalBlocksVerified, "n"); // Can't execute blocks more then committed and proven currently.
```
Change `then` to `than`
___
[Executor.sol: L337](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L337)
```solidity
        require(s.totalBlocksCommitted > _newLastBlock, "v1"); // the last committed block is less new last block
```
Change `less new` to `less than new`
___
[Mailbox.sol: L173](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L173)
```solidity
        // Data that needed for operator to simulate priority queue offchain
```
Change `that needed` to `that is needed` or `needed`
___
[Diamond.sol: L63](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L63)
```solidity
    /// @param initAddress The address that's dellegate called after setting up new facet changes
```
Change `dellegate` to `delegate`
___
[Diamond.sol: L64](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L64)
```solidity
    /// @param initCalldata Calldata for the delegete call to `initAddress`
```
Change `delegete` to `delegate`
___
[Diamond.sol: L255](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L255)
```solidity
    /// NOTE: It is expected but NOT enforced that there are no selectors associated wih `_facet`
```
Change `wih` to `with`
___
[IDiamondCut.sol: L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L3)
```solidity
pragma solidity ^0.8;
```
Change `^0.8;` to ^0.8.0;`

The same typo appears in the following additional contracts:

[IExecutor.sol: L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IExecutor.sol#L3)

[IGovernance.sol: L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IGovernance.sol#L3)

[IMailbox.sol: L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IMailbox.sol#L3)

[IZkSync.sol: L3](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IZkSync.sol#L3)
___
[L1ERC20Bridge.sol: L19](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1ERC20Bridge.sol#L19)
```solidity
/// @dev It is standard implementation of ERC20 Bridge that can be used as a refference
```
Change `refference` to `reference`
___
[L1ERC20Bridge.sol: L76](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1ERC20Bridge.sol#L76)
```solidity
        // We are expecting to see the exect two bytecodes that are needed to initiailize the bridge
```
Change `exect` to `exact`
___
[L2ETHBridge.sol: L58](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ETHBridge.sol#L58)
```solidity
    /// NOTE: In order to get funds on L1, receiver should finalise deposit on L1 counterpart
```
Change `finalise` to `finalize`. Note that the remaining instances of this word in the contract are spelled using American English ("finalize").
___
[L2ETHBridge.sol: L78](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ETHBridge.sol#L78)
```solidity
    /// @notice Address of the L2 token by its L1 couterpart
```
Change `couterpart` to `counterpart`. 

The same typo appears in the following line:

[L2ETHBridge.sol: L83](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ETHBridge.sol#L83)
___
[L2StandardERC20.sol: L42](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2StandardERC20.sol#L42)
```solidity
    /// @dev Stores the L1 address of the bridge and set `name`/`symbol`/`decimls` getters that L1 token has.
```
Change `decimls` to `decimals`
___
___


### Update sensitive terms in both comments and code
Terms incorporating "black," "white," "slave" or "master" are potentially problematic. Substituting more neutral terminology is becoming [common practice](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/). I see that the sensitive term 'whitelist' has been avoided by using 'allowlist'. Two of the contracts still use variations of `master`, which could be converted to 'primary', as shown below.

___
[Mailbox.sol: L205](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L205)
```solidity
                paymaster: uint256(0),
```
Suggestion: Change `paymaster` to `primarypayer`

Similarly for the following instances of `paymaster`:

[IMailbox.sol: L17](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IMailbox.sol#L17)

[IMailbox.sol: L22](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IMailbox.sol#L22)

[IMailbox.sol: L32](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IMailbox.sol#L32)
___
[Mailbox.sol: L210](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L210)
```solidity
                paymasterInput: new bytes(0),
```
Suggestion: Change `paymasterInput` to `primarypayerInput`

Similarly for the following instances of  'paymasterInput`:

[IMailbox.sol: L22](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IMailbox.sol#L22)

[IMailbox.sol: L45](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IMailbox.sol#L45)
___
___

### Missing `require` error message
A `require` message should be included to enable users to understand the reason for failure

[Executor.sol: L43](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L43)
```solidity
        require(expectedNumberOfLayer1Txs == _newBlock.numberOfLayer1Txs);
```
___
[Executor.sol: L45](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L45)
```solidity
        require(l2BlockTimestamp == _newBlock.timestamp);
```
___
[Executor.sol: L297](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L297)
```solidity
        require(_recurisiveAggregationInput.length == 4);
```
___
[L1EthBridge.sol: L145](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1EthBridge.sol#L145)
```solidity
        require(amount != 0);
```
___
[L1EthBridge.sol: L221](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1EthBridge.sol#L221)
```solidity
        require(_message.length == 56);
```
___
[L1EthBridge.sol: L224](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1EthBridge.sol#L224)
```solidity
        require(bytes4(functionSignature) == this.finalizeWithdrawal.selector);
```
___
[L1EthBridge.sol: L238](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1EthBridge.sol#L238)
```solidity
        require(callSuccess);
```
___
[L2StandardERC20.sol: L96](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2StandardERC20.sol#L96)
```solidity
        require(msg.sender == l2Bridge);
```
___
___
