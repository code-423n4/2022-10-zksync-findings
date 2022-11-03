[1] ``<ARRAY>.LENGTH`` SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A`` FOR``-LOOP

The overheads outlined below are *PER LOOP*, excluding the first loop

-storage arrays incur a Gwarmaccess (100 gas)
-memory arrays use MLOAD (3 gas)
-calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset.

*There is 1 instance of this issue:*

```
File: 2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol

111: for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
```
[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L111](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L111)


[2]  USAGE OF ``UINTS/INTS`` SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

*There are 33 instances of this issue*

```
File:  2022-10-zksync/ethereum/contracts/zksync/DiamondInit.sol

30:  uint64 _genesisIndexRepeatedStorageChanges,
```
[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#L30](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#L30)

```
File: 2022-10-zksync/ethereum/contracts/zksync/Storage.sol

40: uint8 l2ShardId;

42: uint16 txNumberInBlock;

54: uint16 txNumberInBlock;
```
[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L40](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L40)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L42](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L42)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L54](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L54)

```
File: 2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol

367:  uint64(0),
```
[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L367](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L367)

```
File:  2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol

92:  uint32

125: uint64 expirationBlock = uint64(block.number + PRIORITY_EXPIRATION);

150:  uint64 _expirationBlock,

169:  layer2Tip: uint192(0)
```
[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L92](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L92)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L125](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L125)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L150](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L150)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L169](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L169)

```
File: 2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol

24:  uint16 selectorPosition;

33:  uint16 facetPosition;

190:  ds.facetToSelectors[_facet].facetPosition = uint16(ds.facets.length);

207: uint16 selectorPosition = uint16(ds.facetToSelectors[_facet].selectors.length);

239:  ds.selectorToFacet[lastSelector].selectorPosition = uint16(selectorPosition);

268:  ds.facetToSelectors[lastFacet].facetPosition = uint16(facetPosition);
```
[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L24](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L24)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L33](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L33)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L190](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L190)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L207](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L207)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L239](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L239)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L268](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L268)

```
File: 2022-10-zksync/ethereum/contracts/zksync/interfaces/IExecutor.sol

16:  uint64 blockNumber;

18:   uint64 indexRepeatedStorageChanges;

41: uint64 blockNumber;

42:  uint64 timestamp;

43:  uint64 indexRepeatedStorageChanges;
```
[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L16](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L16)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L18](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L18)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L41](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L41)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L42](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L42)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L43](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L43)

```
File:  2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol

185: uint16 _l2TxNumberInBlock,

228:  uint16 _l2TxNumberInBlock,

273:  (uint32 functionSignature, uint256 offset) = UnsafeBytes.readUint32(_l2ToL1message, 0);
```

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L185](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L185)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L228](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L228)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L273](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L273)

```
File: 2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol

138:  uint16 _l2TxNumberInBlock,

185:  uint16 _l2TxNumberInBlock,

223:  (uint32 functionSignature, uint256 offset) = UnsafeBytes.readUint32(_message, 0);
```

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L138](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L138)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L185](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L185)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L223](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L223)

```
File: 2022-10-zksync/ethereum/contracts/bridge/interfaces/IL1Bridge.sol

29: uint16 _l2TxNumberInBlock,

36:   uint16 _l2TxNumberInBlock,
```
 [https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L29](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L29)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L36](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L36)

```
File: 2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol

26:  uint160 constant SYSTEM_CONTRACTS_OFFSET = 0x8000;

64:  uint8 version = uint8(_bytecodeHash[0]);
```
[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L26](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L26)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L64](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L64)

```
File: 2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol

29:  uint8 private decimals_;

126: function decimals() public view override returns (uint8) {
```

[https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L29](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L29)

[https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L126](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L126)


```
File:  2022-10-zksync/zksync/contracts/bridge/interfaces/IL1Bridge.sol

10:   uint16 _l2TxNumberInBlock,
```
[https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/interfaces/IL1Bridge.sol#L10](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/interfaces/IL1Bridge.sol#L10)

[3] ``ABI.ENCODE()`` IS LESS EFFICIENT THAN ``ABI.ENCODEPACKED()``

*There are 16 instances of this issue:*

```
File:  2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol

27:  s.diamondCutStorage.proposedDiamondCutHash = keccak256(abi.encode(_facetCuts, _initAddress));

61: keccak256(abi.encode(_diamondCut.facetCuts, _diamondCut.initAddress)),
```
[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L27](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L27)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L61](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L61)

```
File: 2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol

85: return keccak256(abi.encode(_previousBlock.blockHash, _newBlock.newStateRoot));

122:  chainedPriorityTxsHash = keccak256(abi.encode(chainedPriorityTxsHash, canonicalTxHash));

182: concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));

359: return keccak256(abi.encode(passThroughDataHash, metadataHash, auxiliaryOutputHash));

381: return abi.encode(_block.l2LogsTreeRoot, l2ToL1LogsHash, initialStorageChangesHash, repeatedStorageChangesHash);

386:  return keccak256(abi.encode(_storedBlockInfo));

```

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L85](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L85)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L122](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L122)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L182](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L182)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L359](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L359)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L381](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L381)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L386](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L386)


```
File:  2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol

163:  canonicalTxHash = keccak256(abi.encode(transaction));
```
[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L163](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L163)

```
File: 2022-10-zksync/ethereum/contracts/zksync/libraries/Merkle.sol

30: currentHash = keccak256(abi.encode(currentHash, _path[i]));

32:  currentHash = keccak256(abi.encode(_path[i], currentHash));
```

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol#L30](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol#L30)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol#L32](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol#L32)

```
File:  2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol

82:  bytes memory create2Input = abi.encode(address(this), l2ProxyTokenBytecodeHash, _governor);

168:  data = abi.encode(data1, data2, data3);

283:  bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));
```

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L82](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L82)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L168](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L168)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L283](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L283)

```
File:  2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol

58:   bytes memory create2Input = abi.encode(address(this));
```

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L58](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L58)

```
File:  2022-10-zksync/zksync/contracts/bridge/L2ERC20Bridge.sol

114:   bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));
```

[https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L114](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L114)

[4] FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED ``PAYABLE``

If a function modifier such as ``onlyOwner`` is used, the function will revert if a normal user tries to pay the function. Marking the function as ``payable`` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are ``CALLVALUE``(2),``DUP1``(3),``ISZERO``(3),``PUSH``2(3),``JUMPI``(10),``PUSH1``(3),``DUP1``(3),``REVERT``(0),``JUMPDEST``(1),``POP``(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

*There are 21 instances of this issue:*

```
File: 2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol

35:   function cancelDiamondCutProposal() external onlyGovernor {

46:  function executeDiamondCutProposal(Diamond.DiamondCutData calldata _diamondCut) external onlyGovernor {

78:    function emergencyFreezeDiamond() external onlyGovernor {

91:  function unfreezeDiamond() external onlyGovernor {

```
[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L35](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L35)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L46](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L46)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L78](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L78)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L91](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L91)

```
File:  2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol

152-156:  function commitBlocks(StoredBlockInfo memory _lastCommittedBlockData, CommitBlockInfo[] calldata _newBlocksData)
        external
        override
        nonReentrant
        onlyValidator

208:  function executeBlocks(StoredBlockInfo[] calldata _blocksData) external nonReentrant onlyValidator {

221-225: function proveBlocks(
        StoredBlockInfo calldata _prevBlock,
        StoredBlockInfo[] calldata _committedBlocks,
        ProofInput calldata _proof
    ) external nonReentrant onlyValidator {

336: function revertBlocks(uint256 _newLastBlock) external nonReentrant onlyValidator {
```

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L152-L156](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L152-L156)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L208](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L208)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L221-L225](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L221-L225)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L336](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L336)

```
File:  2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol

45:  function setValidator(address _validator, bool _active) external onlyGovernor {

54:  function setL2BootloaderBytecodeHash(bytes32 _l2BootloaderBytecodeHash) external onlyGovernor {

69:  function setL2DefaultAccountBytecodeHash(bytes32 _l2DefaultAccountBytecodeHash) external onlyGovernor {

84:   function setPorterAvailability(bool _zkPorterIsAvailable) external onlyGovernor {

94:  function setVerifier(Verifier _newVerifier) external onlyGovernor {

104:  function setVerifierParams(VerifierParams calldata _newVerifierParams) external onlyGovernor {
```

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L45](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L45)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L54](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L54)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L69](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L69)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L84](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L84)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L94](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L94)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L104](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L104)

```
File:  2022-10-zksync/ethereum/contracts/common/AllowList.sol

57:  function setPublicAccess(address _target, bool _enable) external onlyOwner {

65:    function setBatchPublicAccess(address[] calldata _targets, bool[] calldata _enables) external onlyOwner {

89-94:   function setBatchPermissionToCall(
        address[] calldata _callers,
        address[] calldata _targets,
        bytes4[] calldata _functionSigs,
        bool[] calldata _enables
    ) external onlyOwner {

113-118:  function setPermissionToCall(
        address _caller,
        address _target,
        bytes4 _functionSig,
        bool _enable
    ) external onlyOwner {

140:  function setPendingOwner(address _newPendingOwner) external onlyOwner {
```

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L57](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L57)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L65](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L65)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L89-L94](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L89-L94)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L113-L118](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L113-L118)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L140](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L140)

```
File:  2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol

102:  function bridgeMint(address _to, uint256 _amount) external override onlyBridge {

109:  function bridgeBurn(address _from, uint256 _amount) external override onlyBridge {
```
[https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L102](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L102)

[https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L109](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L109)

[5] USING  ``PRIVATE`` RATHER THAN ``PUBLIC`` FOR CONSTANTS, SAVES GAS

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

*There are 37 instances of this issue:*

```
File:  2022-10-zksync/ethereum/contracts/zksync/Config.sol

5: bytes32 constant EMPTY_STRING_KECCAK = 0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470;

9:  uint256 constant L2_TO_L1_LOG_SERIALIZE_SIZE = 88;

12:  uint256 constant L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;

15:  uint256 constant L2_TO_L1_LOG_MERKLE_TREE_HEIGHT = 9;

20:  bytes32 constant L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH = 
       0x72abee45b59e344af8a6e520241c4744aff26ed411f4c4b00f8af09adada43ba;

23:  uint256 constant INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + 64 * 4896;

26:  uint256 constant REPEATED_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + 40 * 7787;

29:  bytes32 constant DEFAULT_L2_LOGS_TREE_ROOT_HASH = bytes32(0);

32:   address constant L2_TO_L1_MESSENGER = address(0x8008);

35:   address constant L2_BOOTLOADER_ADDRESS = address(0x8001);

38:  address constant L2_KNOWN_CODE_STORAGE_ADDRESS = address(0x8004);

41:  address constant L2_SYSTEM_CONTEXT_ADDRESS = address(0x800b);

44:  uint256 constant PRIORITY_OPERATION_L2_TX_TYPE = 255;

47:  uint256 constant BLOCK_PERIOD = 13 seconds;

51:  uint256 constant PRIORITY_EXPIRATION_PERIOD = 3 days;

54:  uint256 constant PRIORITY_EXPIRATION = $(

60:  uint256 constant UPGRADE_NOTICE_PERIOD = $$(defined(UPGRADE_NOTICE_PERIOD) ? UPGRADE_NOTICE_PERIOD : "14 days");

63:  uint256 constant COMMIT_TIMESTAMP_NOT_OLDER = $$(

69: uint256 constant COMMIT_TIMESTAMP_APPROXIMATION_DELTA = $$(

74: uint256 constant INPUT_MASK = $$(~uint256(0) >> 8);

77:  uint256 constant PRIORITY_TX_MAX_ERGS_LIMIT = 2097152;

80:  uint256 constant SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE = $$(
```
[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L5](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L5)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L9](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L9)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L12](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L12)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L15](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L15)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L20](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L20)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L23](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L23)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L26](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L26)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L29](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L29)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L32](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L32)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L35](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L35)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L38](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L38)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L41](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L41)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L44](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L44)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L47](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L47)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L51](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L51)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L54](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L54)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L60](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L60)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L63](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L63)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L69](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L69)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L74](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L74)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L77](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L77)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L80](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L80)

```
File: 2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol

10-11:   bytes32 constant DIAMOND_INIT_SUCCESS_RETURN_VALUE =
        0x33774e659306e47509050e97cb651e731180a42d458212294d30751925c551a2;

14:  bytes32 constant DIAMOND_STORAGE_POSITION = 0xc8fcad8db84d3cc18b4c41d551ea0ee66dd599cde068d998e57d5e09332c131b;

```
[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L10-L11](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L10-L11)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L14](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L14)

```
File: 2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol

32:  uint256 constant DEPOSIT_ERGS_LIMIT = 2097152;

36:  uint256 constant DEPLOY_L2_BRIDGE_COUNTERPART_ERGS_LIMIT = 2097152;

```
[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L32](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L32)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L36](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L36)

```
File: 2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol

26: uint256 constant DEPOSIT_ERGS_LIMIT = 2097152;

30:   uint256 constant DEPLOY_L2_BRIDGE_COUNTERPART_ERGS_LIMIT = 2097152;

33:  address constant CONVENTIONAL_ETH_ADDRESS = address(0);
```

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L26](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L26)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L30](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L30)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L33](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L33)

```
File:  2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol

26:  uint160 constant SYSTEM_CONTRACTS_OFFSET = 0x8000;

28: address constant BOOTLOADER_ADDRESS = address(SYSTEM_CONTRACTS_OFFSET + 0x01);

30:  address constant DEPLOYER_SYSTEM_CONTRACT_ADDRESS = address(SYSTEM_CONTRACTS_OFFSET + 0x06);

34:  address constant FORCE_DEPLOYER = address(SYSTEM_CONTRACTS_OFFSET + 0x07);

36:  IL2Messenger constant L2_MESSENGER = IL2Messenger(address(SYSTEM_CONTRACTS_OFFSET + 0x08));

38:  address constant VALUE_SIMULATOR_SYSTEM_CONTRACT_ADDRESS = address(SYSTEM_CONTRACTS_OFFSET + 0x09);

41:  bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
```

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L26](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L26)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L28](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L28)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L30](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L30)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L34](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L34)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L36](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L36)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L38](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L38)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L41](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L41)

```
File:  2022-10-zksync/zksync/contracts/bridge/L2ETHBridge.sol

28:  address constant CONVENTIONAL_ETH_ADDRESS = address(0);
```

[https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L28](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L28)

[6]  USE ASSEMBLY TO CHECK FOR ADDRESS(0) to save gas.

*There are 8 instances of this issue:*

```
File: 2022-10-zksync/ethereum/contracts/zksync/DiamondProxy.sol

29:  require(facetAddress != address(0), "F");
```
[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L29](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L29)

```
File: 2022-10-zksync/ethereum/contracts/zksync/facets/Getters.sol

148:  require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");
```

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Getters.sol#L148](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Getters.sol#L148)


```
File: 2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol

126: require(_facet != address(0), "G");

150: require(_facet != address(0), "K");

156: require(oldFacet.facetAddress != address(0), "L");

176:  require(oldFacet.facetAddress != address(0), "a2");
```

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L126](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L126)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L150](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L150)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L156](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L156)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L176](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L176)

```
File:  2022-10-zksync/zksync/contracts/bridge/L2ERC20Bridge.sol

95:  require(l1Token != address(0), "yh");
```
[https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L95](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L95)

```
File:  2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol

45: require(_l1Address != address(0), "in6");
```

[https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L45](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L45)

[7] SPLITTING ``REQUIRE()`` STATEMENTS THAT USE ``&&`` SAVES GAS

See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper.

*There are 2 instances of this issue:*

```
File:  2022-10-zksync/ethereum/contracts/common/AllowList.sol

96-101:  require(
            callersLength == _targets.length &&
                callersLength == _functionSigs.length &&
                callersLength == _enables.length,
            "yw"
        );
```

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L96-L101](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L96-L101)


```
File: 2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol

65: require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf");
```

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L65](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L65)

[8]  DUPLICATED ``REQUIRE()``/``REVERT()`` CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION

Saves deployment cost

*There are 2 instances of this issue:*

```
File:  2022-10-zksync/ethereum/contracts/zksync/libraries/PriorityQueue.sol

64:  require(!_queue.isEmpty(), "D");

72:   require(!_queue.isEmpty(), "s");

```
[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L64](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L64)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L72](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L72)

[9] ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS
 
The ``unchecked`` keyword is new in solidity 0.8.0,so this only applies to that version or higher, which these instances are. This saves 30-40 gas *[PER LOOP](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)*

*There are 4 instances of this issue:*

```
File:  2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol

94:   for (uint256 i = 0; i < facetCutsLength; ++i) {

132:  for (uint256 i = 0; i < selectorsLength; ++i) {

153:  for (uint256 i = 0; i < selectorsLength; ++i) {

173:  for (uint256 i = 0; i < selectorsLength; ++i) {
```

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L94](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L94)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L132](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L132)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L153](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L153)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L173](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L173)








































