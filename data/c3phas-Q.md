## QA

### Missing checks for address(0x0) when assigning values to address state variables

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ERC20Bridge.sol#L36
```solidity
File: /zksync/contracts/bridge/L2ERC20Bridge.sol
36:        l1Bridge = _l1Bridge;

```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ETHBridge.sol#L31

```solidity
File: /zksync/contracts/bridge/L2ETHBridge.sol
31:        l1Bridge = _l1Bridge;
```
### Constants should be defined rather than using magic numbers
There are several occurrences of literal values with unexplained meaning .Literal values in the codebase without an explained meaning make the code harder to read, understand and maintain, thus hindering the experience of developers, auditors and external contributors alike.

Developers should define a constant variable for every magic value used , giving it a clear and self-explanatory name

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/L2ContractHelper.sol#L50
```solidity
File: /ethereum/contracts/common/L2ContractHelper.sol

//@audit: 32
50:        require(_bytecode.length % 32 == 0, "po");

//@audit: 32
52:        uint256 bytecodeLenInWords = _bytecode.length / 32;


//@audit: 248
57:        hashedBytecode = (hashedBytecode | bytes32(uint256(1 << 248)));

//@audit: 224
59:        hashedBytecode = hashedBytecode | bytes32(bytecodeLenInWords << 224);

//@audit: 256
72:        codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));

```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L271
```solidity
File: /ethereum/contracts/bridge/L1ERC20Bridge.sol

//@audit: 76
271:        require(_l2ToL1message.length == 76, "kk");
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L221
```solidity
File: /ethereum/contracts/bridge/L1EthBridge.sol

//@audit: 56
221:        require(_message.length == 56);
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Merkle.sol#L24
```solidity
File: /ethereum/contracts/zksync/libraries/Merkle.sol

//@audit: 256
24:        require(pathLength < 256, "bt");
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L287
```solidity
File: /ethereum/contracts/zksync/libraries/Diamond.sol

//@audit: 32
287:            require(data.length == 32, "lp");
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L310-L311
```solidity
File: /ethereum/contracts/zksync/facets/Executor.sol
310: 0x198e9393920d483a7260bfb731fb5d25f1aa493335a9e71297e485b7aef312c2,            311: 0x1800deef121f1e76426a00665e5c4479674322d4f75edadd46debd5cd992f6ed

314: 0x090689d0585ff075ec9e99ad690c3395bc4b313370b38ef355acdadcd122975b,                315: 0x12c85ea5db8c6deb4aab71808dcb408fe3d1e7690c43d37b4ce6cc0166fa7daa

320: 0x260e01b251f6f1c7e7ff4e580791dee8ea51d87a358e038b4efe30fac09383c1,                321: 0x0118c4d5b837bcc2bc89b5b398b5974e9f5944073b32078b7e231fec938883b0

324: 0x04fc6369f7110fe3d25156c1bb9a72859cf2a04641f99ba4ee413c80da6a5fe4,               325: 0x22febda3c0c0632a56475b4214e5615e11e6dd3f96e6cea2854a87d4dacc5e55
```
### Public functions not called by the contract should be declared external instead
Contracts are allowed to override their parents' functions and change the visibility from external to public.
Functions marked by external use call data to read arguments, where public will first allocate in local memory and then load them.

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ETHBridge.sol#L79
```solidity
File: /zksync/contracts/bridge/L2ETHBridge.sol
79:    function l2TokenAddress(address) public pure returns (address) {
```

### abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). Unless there is a compelling reason, abi.encode should be preferred. 

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L282-L291
```solidity
File: /ethereum/contracts/zksync/facets/Executor.sol
282:                keccak256(
283:                    abi.encodePacked(
284:                        _prevBlockCommitment,
285:                        _currentBlockCommitment,
286:                        _verifierParams.recursionNodeLevelVkHash,
287:                        _verifierParams.recursionLeafLevelVkHash,
288:                        _verifierParams.recursionCircuitsSetVksHash,
289:                        _proof.recurisiveAggregationInput
290:                    )
291:                )
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Mailbox.sol#L58-L60
```solidity
File: /ethereum/contracts/zksync/facets/Mailbox.sol
58:        bytes32 hashedLog = keccak256(
59:            abi.encodePacked(_log.l2ShardId, _log.isService, _log.txNumberInBlock, _log.sender, _log.key, _log.value)
60:        );
```
### Typos

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L76
```solidity
File: /ethereum/contracts/bridge/L1ERC20Bridge.sol

//@audit: exect instead of exact
76:        // We are expecting to see the exect two bytecodes that are needed to initiailize the bridge
```
### Open todos
https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Config.sol#L28
```solidity
File: /ethereum/contracts/zksync/Config.sol
28:// TODO: change constant to the real root hash of empty Merkle tree (SMA-184)
```

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L94
```solidity
File: /ethereum/contracts/zksync/facets/Mailbox.sol
94:        // TODO: estimate gas for L1 execute

127:        // TODO: Restore after stable priority op fee modeling. (SMA-1230)

169:                layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)
```

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IExecutor.sol#L56
```solidity
File: /ethereum/contracts/zksync/interfaces/IExecutor.sol
56:    /// TODO: The verifier integration is not finished yet, change the structure for compatibility later
```

### Natspec is incomplete
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ERC20Bridge.sol#L112-L113
```solidity
File:/zksync/contracts/bridge/L2ERC20Bridge.sol

//@audit: Missing @param _l1Token
112:    /// @return Address of an L2 token counterpart
113:    function l2TokenAddress(address _l1Token) public view override returns (address) {
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ETHBridge.sol#L57-L63
```solidity
File: /zksync/contracts/bridge/L2ETHBridge.sol

//@audit: Missing @param _l1Receiver, @param _l2Token, @param _amount

57:    /// @dev Initiate withdrawal ethers from L2 contract to the L1
58:    /// NOTE: In order to get funds on L1, receiver should finalise deposit on L1 counterpart
59:    function withdraw(
60:        address _l1Receiver,
61:        address _l2Token,
62:        uint256 _amount
63:    ) external override {
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2StandardERC20.sol#L41-L43
```solidity
File: /zksync/contracts/bridge/L2StandardERC20.sol

//@audit: Missing @param _l1Address, @param _data

41:    /// @notice Initializes a contract token for later use. Expected to be used in the proxy.
42:    /// @dev Stores the L1 address of the bridge and set `name`/`symbol`/`decimls` getters that L1 token has.
43:    function bridgeInitialize(address _l1Address, bytes memory _data) external initializer {


//@audit: Missing @param _to, @param _amount

100:    /// @dev Mint tokens to a given account.
101:    /// @notice Should be called by bridge after depositing tokens from L1.
102:    function bridgeMint(address _to, uint256 _amount) external override onlyBridge {

//@audit: Missing @param _from, @param _amount

107:    /// @dev Burn tokens from a given account.
108:    /// @notice Should be called by bridge before withdrawing tokens to L1.
109:    function bridgeBurn(address _from, uint256 _amount) external override onlyBridge {
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/DiamondInit.sol#L25-L37
```solidity
File: /ethereum/contracts/zksync/DiamondInit.sol

//@audit: Missing @param _allowList, @param _verifierParams, @param _zkPorterIsAvailable, @param _l2BootloaderBytecodeHash, @param _l2DefaultAccountBytecodeHash
25:    function initialize(
26:        Verifier _verifier,
27:        address _governor,
28:        address _validator,
29:        bytes32 _genesisBlockHash,
30:        uint64 _genesisIndexRepeatedStorageChanges,
31:        bytes32 _genesisBlockCommitment,
32:        IAllowList _allowList,
33:        VerifierParams calldata _verifierParams,
34:        bool _zkPorterIsAvailable,
35:        bytes32 _l2BootloaderBytecodeHash,
36:        bytes32 _l2DefaultAccountBytecodeHash
37:    ) external reentrancyGuardInitializer returns (bytes32) {
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Getters.sol#L73-L74
```solidity
File: /ethereum/contracts/zksync/facets/Getters.sol

//@audit: Missing @param _address
73:    /// @return Whether the address has a validator access
74:    function isValidator(address _address) external view returns (bool) {

//@audit: Missing @param _blockNumber
78:    /// @return Merkle root of the tree with L2 logs for the selected block
79:    function l2LogsRootHash(uint256 _blockNumber) external view returns (bytes32) {

//@audit: Missing @param _blockNumber
86:    function storedBlockHash(uint256 _blockNumber) external view returns (bytes32) {

//@audit: Missing @param _address
116:    function isSecurityCouncilMember(address _address) external view returns (bool) {

//@audit: Missing @param _address
122:    function getSecurityCouncilMemberLastApprovedProposalId(address _address) external view returns (uint256) {

//@audit: Missing @param _facet
133:    function isFacetFreezable(address _facet) external view returns (bool isFreezable) {

//@audit: Missing @param _selector
146:    function isFunctionFreezable(bytes4 _selector) external view returns (bool) {

//@audit: Missing @param _facet
172:    function facetFunctionSelectors(address _facet) external view returns (bytes4[] memory) {

//@audit: Missing @param _selector
184:    function facetAddress(bytes4 _selector) external view returns (address) {
```
### Missing friendly revert strings
 require()/revert() statements should have descriptive reason strings.

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Executor.sol#L43-L45
```solidity
File: /ethereum/contracts/zksync/facets/Executor.sol
43:        require(expectedNumberOfLayer1Txs == _newBlock.numberOfLayer1Txs);
44:        // Check that the timestamp that came from the Bootloader is expected
45:        require(l2BlockTimestamp == _newBlock.timestamp);


297:        require(_recurisiveAggregationInput.length == 4);
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L145
```solidity
File: /ethereum/contracts/bridge/L1EthBridge.sol
145:        require(amount != 0);

221:        require(_message.length == 56);

224:        require(bytes4(functionSignature) == this.finalizeWithdrawal.selector);
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ETHBridge.sol#L50
```solidity
File: /zksync/contracts/bridge/L2ETHBridge.sol
50:        require(_l1Token == CONVENTIONAL_ETH_ADDRESS);
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2StandardERC20.sol#L96
```solidity
File: /zksync/contracts/bridge/L2StandardERC20.sol
96:        require(msg.sender == l2Bridge);
```

### Commented code should be deleted
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Mailbox.sol#L128-L129
```solidity
File: /ethereum/contracts/zksync/facets/Mailbox.sol
128:        // uint256 baseCost = l2TransactionBaseCost(tx.gasprice, _ergsLimit, uint32(_calldata.length));
129:        // uint256 layer2Tip = msg.value - baseCost;
```

### Lack of event emission after critical initialize() functions

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L71-L103
```solidity
File: /ethereum/contracts/bridge/L1ERC20Bridge.sol
71:    function initialize(
72:        bytes[] calldata _factoryDeps,
73:        address _l2TokenFactory,
74:        address _governor
75:    ) external reentrancyGuardInitializer {
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L56-L80
```solidity
File: /ethereum/contracts/bridge/L1EthBridge.sol
56:    function initialize(bytes calldata _l2BridgeBytecode) external reentrancyGuardInitializer {
```
### Event is missing `indexed` fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IMailbox.sol#L95-L101
```solidity
File: /ethereum/contracts/zksync/interfaces/IMailbox.sol
95:    event NewPriorityRequest(
96:        uint256 txId,
97:        bytes32 txHash,
98:        uint64 expirationBlock,
99:        L2CanonicalTransaction transaction,
100:        bytes[] factoryDeps
101:    );
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IGovernance.sol#L48
```solidity
File: /ethereum/contracts/zksync/interfaces/IGovernance.sol
48:    event NewVerifierParams(VerifierParams oldVerifierParams, VerifierParams newVerifierParams);
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/interfaces/IExecutor.sol#L85
```solidity
File: /ethereum/contracts/zksync/interfaces/IExecutor.sol
85:    event BlocksRevert(uint256 totalBlocksCommitted, uint256 totalBlocksVerified, uint256 totalBlocksExecuted);
```

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Diamond.sol#L16
```solidity
File: /ethereum/contracts/zksync/libraries/Diamond.sol
16:    event DiamondCut(FacetCut[] facetCuts, address initAddress, bytes initCalldata);
```

### Code Structure Deviates From Best-Practice
The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. 
Function ordering helps readers identify which functions they can call and find constructor and fallback functions easier.
Functions should be grouped according to their visibility and ordered as: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. Some constructs deviate from this recommended best-practice: 

External/public functions are mixed with internal/private ones

The following function is an internal function which is in the middle of public and external functions
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ERC20Bridge.sol#L73
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ERC20Bridge.sol#L104

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2ETHBridge.sol#L74

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/AllowList.sol#L75
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/AllowList.sol#L123

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L260

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L232

**Event declared before state variables**
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2StandardERC20.sol#L12

**Modifier declared in between functions**
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2StandardERC20.sol#L95


 **Recommendation:**
Consider adopting recommended best-practice for code structure and layout.

See: [https://docs.soliditylang.org/en/v0.8.15/style-guide.html#order-of-layout](https://docs.soliditylang.org/en/v0.8.15/style-guide.html#order-of-layout)

