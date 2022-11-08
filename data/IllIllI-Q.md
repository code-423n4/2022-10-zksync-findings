## Summary


### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | `require()` should be used instead of `assert()` | 1 |
| [L&#x2011;02] | Missing checks for `address(0x0)` when assigning values to `address` state variables | 3 |
| [L&#x2011;03] | Open TODOs | 4 |

Total: 8 instances over 3 issues

### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions | 1 |
| [N&#x2011;02] | `require()`/`revert()` statements should have descriptive reason strings | 12 |
| [N&#x2011;03] | `public` functions not called by the contract should be declared `external` instead | 4 |
| [N&#x2011;04] | `2**<n> - 1` should be re-written as `type(uint<n>).max` | 1 |
| [N&#x2011;05] | Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant` | 2 |
| [N&#x2011;06] | Lines are too long | 5 |
| [N&#x2011;07] | File is missing NatSpec | 11 |
| [N&#x2011;08] | NatSpec is incomplete | 29 |
| [N&#x2011;09] | Event is missing `indexed` fields | 17 |
| [N&#x2011;10] | Not using the named return variables anywhere in the function is confusing | 1 |

Total: 83 instances over 10 issues

## Low Risk Issues

### [L&#x2011;01]  `require()` should be used instead of `assert()`
Prior to solidity version 0.8.0, hitting an assert consumes the **remainder of the transaction's available gas** rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that "The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix".

*There is 1 instance of this issue:*
```solidity
File: ethereum/contracts/zksync/facets/DiamondCut.sol

16:           assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/DiamondCut.sol#L16

### [L&#x2011;02]  Missing checks for `address(0x0)` when assigning values to `address` state variables

*There are 3 instances of this issue:*
```solidity
File: ethereum/contracts/bridge/L1ERC20Bridge.sol

79:           l2TokenFactory = _l2TokenFactory;

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L79

```solidity
File: zksync/contracts/bridge/L2ERC20Bridge.sol

36:           l1Bridge = _l1Bridge;

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ERC20Bridge.sol#L36

```solidity
File: zksync/contracts/bridge/L2ETHBridge.sol

31:           l1Bridge = _l1Bridge;

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ETHBridge.sol#L31

### [L&#x2011;03]  Open TODOs
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

*There are 4 instances of this issue:*
```solidity
File: ethereum/contracts/zksync/facets/Mailbox.sol

94:           // TODO: estimate gas for L1 execute

127:          // TODO: Restore after stable priority op fee modeling. (SMA-1230)

169:                  layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Mailbox.sol#L94

```solidity
File: ethereum/contracts/zksync/interfaces/IExecutor.sol

56:       /// TODO: The verifier integration is not finished yet, change the structure for compatibility later

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IExecutor.sol#L56

## Non-critical Issues

### [N&#x2011;01]  Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions
See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

*There is 1 instance of this issue:*
```solidity
File: zksync/contracts/bridge/L2StandardERC20.sol

11:   contract L2StandardERC20 is ERC20Upgradeable, IL2StandardToken {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2StandardERC20.sol#L11

### [N&#x2011;02]  `require()`/`revert()` statements should have descriptive reason strings

*There are 12 instances of this issue:*
```solidity
File: ethereum/contracts/bridge/L1EthBridge.sol

145:          require(amount != 0);

221:          require(_message.length == 56);

224:          require(bytes4(functionSignature) == this.finalizeWithdrawal.selector);

238:          require(callSuccess);

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L145

```solidity
File: ethereum/contracts/zksync/facets/Executor.sol

43:           require(expectedNumberOfLayer1Txs == _newBlock.numberOfLayer1Txs);

45:           require(l2BlockTimestamp == _newBlock.timestamp);

297:          require(_recurisiveAggregationInput.length == 4);

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L43

```solidity
File: zksync/contracts/bridge/L2ETHBridge.sol

50:           require(_l1Token == CONVENTIONAL_ETH_ADDRESS);

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ETHBridge.sol#L50

```solidity
File: zksync/contracts/bridge/L2StandardERC20.sol

96:           require(msg.sender == l2Bridge);

116:          if (availableGetters.ignoreName) revert();

122:          if (availableGetters.ignoreSymbol) revert();

128:          if (availableGetters.ignoreDecimals) revert();

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2StandardERC20.sol#L96

### [N&#x2011;03]  `public` functions not called by the contract should be declared `external` instead
Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents' functions and change the visibility from `external` to `public`.

*There are 4 instances of this issue:*
```solidity
File: ethereum/contracts/bridge/L1ERC20Bridge.sol

282:      function l2TokenAddress(address _l1Token) public view returns (address) {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L282

```solidity
File: ethereum/contracts/bridge/L1EthBridge.sol

243:      function l2TokenAddress(address) public pure returns (address) {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L243

```solidity
File: ethereum/contracts/zksync/facets/Mailbox.sol

89        function l2TransactionBaseCost(
90            uint256, // _gasPrice
91            uint256, // _ergsLimit
92            uint32 // _calldataLength
93:       ) public pure returns (uint256) {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Mailbox.sol#L89-L93

```solidity
File: zksync/contracts/bridge/L2ETHBridge.sol

79:       function l2TokenAddress(address) public pure returns (address) {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ETHBridge.sol#L79

### [N&#x2011;04]  `2**<n> - 1` should be re-written as `type(uint<n>).max`
Earlier versions of solidity can use `uint<n>(-1)` instead. Expressions not including the `- 1` can often be re-written to accomodate the change (e.g. by using a `>` rather than a `>=`, which will also save some gas)

*There is 1 instance of this issue:*
```solidity
File: ethereum/contracts/common/L2ContractHelper.sol

53:           require(bytecodeLenInWords < 2**16, "pp"); // bytecode length must be less than 2^16 words

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/L2ContractHelper.sol#L53

### [N&#x2011;05]  Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`
While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the constructor.

*There are 2 instances of this issue:*
```solidity
File: ethereum/contracts/common/L2ContractHelper.sol

41:       bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/L2ContractHelper.sol#L41

```solidity
File: zksync/contracts/L2ContractHelper.sol

24:       bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/L2ContractHelper.sol#L24

### [N&#x2011;06]  Lines are too long
Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

*There are 5 instances of this issue:*
```solidity
File: ethereum/contracts/zksync/facets/DiamondCut.sol

104:      /// @param _diamondCutHash The hash of the diamond cut that security council members want to approve. Needed to prevent unintentional approvals, including reorg attacks

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/DiamondCut.sol#L104

```solidity
File: ethereum/contracts/zksync/interfaces/IMailbox.sol

15:       /// @param maxFeePerErg The absolute maximum sender willing to pay per unit of ergs to get the transaction included in a block. Analog to the EIP-1559 `maxFeePerGas` on an L1 transactions

16:       /// @param maxPriorityFeePerErg The additional fee that is paid directly to the validator to incentivize them to include the transaction in a block. Analog to the EIP-1559 `maxPriorityFeePerGas` on an L1 transactions

17:       /// @param paymaster The address of the EIP-4337 paymaster, that will pay fees for the transaction. `uint256` type for possible address format changes and maintaining backward compatibility

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IMailbox.sol#L15

```solidity
File: ethereum/contracts/zksync/libraries/Diamond.sol

14:       bytes32 constant DIAMOND_STORAGE_POSITION = 0xc8fcad8db84d3cc18b4c41d551ea0ee66dd599cde068d998e57d5e09332c131b; // keccak256("diamond.standard.diamond.storage") - 1;

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L14

### [N&#x2011;07]  File is missing NatSpec

*There are 11 instances of this issue:*
```solidity
File: ethereum/contracts/bridge/interfaces/IL1Bridge.sol


```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/interfaces/IL1Bridge.sol

```solidity
File: ethereum/contracts/bridge/interfaces/IL2Bridge.sol


```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/interfaces/IL2Bridge.sol

```solidity
File: ethereum/contracts/common/AllowListed.sol


```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/AllowListed.sol

```solidity
File: ethereum/contracts/common/libraries/UncheckedMath.sol


```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/libraries/UncheckedMath.sol

```solidity
File: ethereum/contracts/zksync/interfaces/IDiamondCut.sol


```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IDiamondCut.sol

```solidity
File: ethereum/contracts/zksync/interfaces/IZkSync.sol


```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IZkSync.sol

```solidity
File: zksync/contracts/bridge/interfaces/IL1Bridge.sol


```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/interfaces/IL1Bridge.sol

```solidity
File: zksync/contracts/bridge/interfaces/IL2Bridge.sol


```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/interfaces/IL2Bridge.sol

```solidity
File: zksync/contracts/bridge/interfaces/IL2EthInitializable.sol


```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/interfaces/IL2EthInitializable.sol

```solidity
File: zksync/contracts/bridge/interfaces/IL2StandardToken.sol


```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/interfaces/IL2StandardToken.sol

```solidity
File: zksync/contracts/L2ContractHelper.sol


```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/L2ContractHelper.sol

### [N&#x2011;08]  NatSpec is incomplete

*There are 29 instances of this issue:*
```solidity
File: ethereum/contracts/bridge/L1ERC20Bridge.sol

/// @audit Missing: '@param _from'
/// @audit Missing: '@param _token'
/// @audit Missing: '@param _amount'
134       /// @dev Transfers tokens from the depositor address to the smart contract address
135       /// @return The difference between the contract balance before and after the transferring funds
136       function _depositFunds(
137           address _from,
138           IERC20 _token,
139           uint256 _amount
140:      ) internal returns (uint256) {

/// @audit Missing: '@param _l1Token'
281       /// @return The L2 token address that would be minted for deposit of the given L1 token
282:      function l2TokenAddress(address _l1Token) public view returns (address) {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L134-L140

```solidity
File: ethereum/contracts/zksync/DiamondInit.sol

/// @audit Missing: '@param _allowList'
/// @audit Missing: '@param _verifierParams'
/// @audit Missing: '@param _zkPorterIsAvailable'
/// @audit Missing: '@param _l2BootloaderBytecodeHash'
/// @audit Missing: '@param _l2DefaultAccountBytecodeHash'
17        /// @notice zkSync contract initialization
18        /// @param _verifier address of Verifier contract
19        /// @param _governor address who can manage the contract
20        /// @param _validator address who can make blocks
21        /// @param _genesisBlockHash Block hash of the genesis (initial) block
22        /// @param _genesisIndexRepeatedStorageChanges The serial number of the shortcut storage key for genesis block
23        /// @param _genesisBlockCommitment The zk-proof commitment for the genesis block
24        /// @return Magic 32 bytes, which indicates that the contract logic is expected to be used as a diamond proxy initializer
25        function initialize(
26            Verifier _verifier,
27            address _governor,
28            address _validator,
29            bytes32 _genesisBlockHash,
30            uint64 _genesisIndexRepeatedStorageChanges,
31            bytes32 _genesisBlockCommitment,
32            IAllowList _allowList,
33            VerifierParams calldata _verifierParams,
34            bool _zkPorterIsAvailable,
35            bytes32 _l2BootloaderBytecodeHash,
36            bytes32 _l2DefaultAccountBytecodeHash
37:       ) external reentrancyGuardInitializer returns (bytes32) {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/DiamondInit.sol#L17-L37

```solidity
File: ethereum/contracts/zksync/facets/Getters.sol

/// @audit Missing: '@param _address'
73        /// @return Whether the address has a validator access
74:       function isValidator(address _address) external view returns (bool) {

/// @audit Missing: '@param _blockNumber'
78        /// @return Merkle root of the tree with L2 logs for the selected block
79:       function l2LogsRootHash(uint256 _blockNumber) external view returns (bytes32) {

/// @audit Missing: '@param _blockNumber'
83        /// @notice For unfinalized (non executed) blocks may change
84        /// @dev returns zero for non-committed blocks
85        /// @return The hash of committed L2 block.
86:       function storedBlockHash(uint256 _blockNumber) external view returns (bytes32) {

/// @audit Missing: '@param _address'
115       /// @return Whether the address is a member of security council
116:      function isSecurityCouncilMember(address _address) external view returns (bool) {

/// @audit Missing: '@param _address'
120       /// @notice Returns zero for not security council members
121       /// @return The index of the last diamond cut that security member approved
122:      function getSecurityCouncilMemberLastApprovedProposalId(address _address) external view returns (uint256) {

/// @audit Missing: '@param _facet'
132       /// @return isFreezable Whether the facet can be frozen by the governor or always accessible
133:      function isFacetFreezable(address _facet) external view returns (bool isFreezable) {

/// @audit Missing: '@param _selector'
145       /// @return Whether the selector can be frozen by the governor or always accessible
146:      function isFunctionFreezable(bytes4 _selector) external view returns (bool) {

/// @audit Missing: '@param _facet'
171       /// @return NON-sorted array with function selectors supported by a specific facet
172:      function facetFunctionSelectors(address _facet) external view returns (bytes4[] memory) {

/// @audit Missing: '@param _selector'
183       /// @return Facet address associated with a selector. Zero if the selector is not added to the diamond
184:      function facetAddress(bytes4 _selector) external view returns (address) {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Getters.sol#L73-L74

```solidity
File: ethereum/contracts/zksync/facets/Mailbox.sol

/// @audit Missing: '@return'
38        /// @param _log Information about the sent log
39        /// @param _proof Merkle proof for inclusion of the L2 log
40        function proveL2LogInclusion(
41            uint256 _blockNumber,
42            uint256 _index,
43            L2Log memory _log,
44            bytes32[] calldata _proof
45:       ) external view returns (bool) {

/// @audit Missing: '@param uint256'
/// @audit Missing: '@param uint32'
87        /// @notice Estimates the cost in Ether of requesting execution of an L2 transaction from L1
88        /// @return The estimated ergs
89        function l2TransactionBaseCost(
90            uint256, // _gasPrice
91            uint256, // _ergsLimit
92            uint32 // _calldataLength
93:       ) public pure returns (uint256) {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Mailbox.sol#L38-L45

```solidity
File: ethereum/contracts/zksync/libraries/PriorityQueue.sol

/// @audit Missing: '@param _queue'
32        /// @notice Returns zero if and only if no operations were processed from the queue
33        /// @return Index of the oldest priority operation that wasn't processed yet
34:       function getFirstUnprocessedPriorityTx(Queue storage _queue) internal view returns (uint256) {

/// @audit Missing: '@param _queue'
38        /// @return The total number of priority operations that were added to the priority queue, including all processed ones
39:       function getTotalPriorityTxs(Queue storage _queue) internal view returns (uint256) {

/// @audit Missing: '@param _queue'
43        /// @return The total number of unprocessed priority operations in a priority queue
44:       function getSize(Queue storage _queue) internal view returns (uint256) {

/// @audit Missing: '@param _queue'
48        /// @return Whether the priority queue contains no operations
49:       function isEmpty(Queue storage _queue) internal view returns (bool) {

/// @audit Missing: '@param _queue'
62        /// @return The first unprocessed priority operation from the queue
63:       function front(Queue storage _queue) internal view returns (PriorityOperation memory) {

/// @audit Missing: '@param _queue'
69        /// @notice Remove the first unprocessed priority operation from the queue
70        /// @return priorityOperation that was popped from the priority queue
71:       function popFront(Queue storage _queue) internal returns (PriorityOperation memory priorityOperation) {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L32-L34

```solidity
File: zksync/contracts/bridge/L2ERC20Bridge.sol

/// @audit Missing: '@param _l1Token'
112       /// @return Address of an L2 token counterpart
113:      function l2TokenAddress(address _l1Token) public view override returns (address) {

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ERC20Bridge.sol#L112-L113

```solidity
File: zksync/contracts/bridge/L2ETHBridge.sol

/// @audit Missing: '@param bytes'
36        /// @dev Finalize the deposit and mint ether to the deposited address
37        /// @param _l1Sender The account address that initiate the deposit on L1
38        /// @param _l2Receiver The account address that would receive minted ether
39        /// @param _l1Token The address of the token that was locked on the L1. Always should be equal to zero (conventional value)
40        /// @param _amount Total amount of ether deposited from L1
41        function finalizeDeposit(
42            address _l1Sender,
43            address _l2Receiver,
44            address _l1Token,
45            uint256 _amount,
46:           bytes calldata // _data

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ETHBridge.sol#L36-L46

### [N&#x2011;09]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 17 instances of this issue:*
```solidity
File: ethereum/contracts/bridge/interfaces/IL1Bridge.sol

11:       event WithdrawalFinalized(address indexed to, address indexed l1Token, uint256 amount);

13:       event ClaimedFailedDeposit(address indexed to, address indexed l1Token, uint256 amount);

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L11

```solidity
File: ethereum/contracts/common/interfaces/IAllowList.sol

11:       event UpdatePublicAccess(address indexed target, bool newStatus);

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/interfaces/IAllowList.sol#L11

```solidity
File: ethereum/contracts/zksync/interfaces/IDiamondCut.sol

20:       event DiamondCutProposal(Diamond.FacetCut[] _facetCuts, address _initAddress);

22:       event DiamondCutProposalCancelation(uint256 currentProposalId, bytes32 indexed proposedDiamondCutHash);

24:       event DiamondCutProposalExecution(Diamond.DiamondCutData _diamondCut);

28:       event Unfreeze(uint256 lastDiamondFreezeTimestamp);

30        event EmergencyDiamondCutApproved(
31            address indexed _address,
32            uint256 currentProposalId,
33            uint256 securityCouncilEmergencyApprovals,
34            bytes32 indexed proposedDiamondCutHash
35:       );

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L20

```solidity
File: ethereum/contracts/zksync/interfaces/IExecutor.sol

85:       event BlocksRevert(uint256 totalBlocksCommitted, uint256 totalBlocksVerified, uint256 totalBlocksExecuted);

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IExecutor.sol#L85

```solidity
File: ethereum/contracts/zksync/interfaces/IGovernance.sol

32:       event IsPorterAvailableStatusUpdate(bool isPorterAvailable);

35:       event ValidatorStatusUpdate(address indexed validatorAddress, bool isActive);

48:       event NewVerifierParams(VerifierParams oldVerifierParams, VerifierParams newVerifierParams);

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IGovernance.sol#L32

```solidity
File: ethereum/contracts/zksync/interfaces/IMailbox.sol

95        event NewPriorityRequest(
96            uint256 txId,
97            bytes32 txHash,
98            uint64 expirationBlock,
99            L2CanonicalTransaction transaction,
100           bytes[] factoryDeps
101:      );

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IMailbox.sol#L95-L101

```solidity
File: ethereum/contracts/zksync/libraries/Diamond.sol

16:       event DiamondCut(FacetCut[] facetCuts, address initAddress, bytes initCalldata);

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L16

```solidity
File: zksync/contracts/bridge/interfaces/IL2StandardToken.sol

6:        event BridgeMint(address indexed _account, uint256 _amount);

8:        event BridgeBurn(address indexed _account, uint256 _amount);

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/interfaces/IL2StandardToken.sol#L6

```solidity
File: zksync/contracts/bridge/L2StandardERC20.sol

12:       event BridgeInitialization(address indexed l1Token, string name, string symbol, uint8 decimals);

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2StandardERC20.sol#L12

### [N&#x2011;10]  Not using the named return variables anywhere in the function is confusing
Consider changing the variable to be an unnamed one

*There is 1 instance of this issue:*
```solidity
File: ethereum/contracts/zksync/facets/Executor.sol

/// @audit storedNewBlock
23        function _commitOneBlock(StoredBlockInfo memory _previousBlock, CommitBlockInfo calldata _newBlock)
24            internal
25            view
26:           returns (StoredBlockInfo memory storedNewBlock)

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L23-L26



