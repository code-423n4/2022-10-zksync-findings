
## [G01] `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables

#### Instances:
```
/ethereum/contracts/zksync/facets/DiamondCut.sol::29 => s.diamondCutStorage.currentProposalId += 1;
```

## [G02] Splitting `require()` statements that use `&&` saves gas

See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper

#### Instances:
```
/ethereum/contracts/common/L2ContractHelper.sol::65 => require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
```

## [G03] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.\[layout_in_storage.html]{https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html}\Use a larger size then downcast where needed

#### Instances:
```
/ethereum/contracts/bridge/L1ERC20Bridge.sol::273 => (uint32 functionSignature, uint256 offset) = UnsafeBytes.readUint32(_l2ToL1message, 0);
/ethereum/contracts/bridge/L1EthBridge.sol::223 => (uint32 functionSignature, uint256 offset) = UnsafeBytes.readUint32(_message, 0);
/ethereum/contracts/common/L2ContractHelper.sol::26 => uint160 constant SYSTEM_CONTRACTS_OFFSET = 0x8000; // 2^15
/ethereum/contracts/common/L2ContractHelper.sol::64 => uint8 version = uint8(_bytecodeHash[0]);
/ethereum/contracts/zksync/libraries/Diamond.sol::207 => uint16 selectorPosition = uint16(ds.facetToSelectors[_facet].selectors.length);
/zksync/contracts/L2ContractHelper.sol::17 => uint160 constant SYSTEM_CONTRACTS_OFFSET = 0x8000; // 2^15
```

## [G04] abi.encode() is less efficient than abi.encodePacked()

#### Instances:
```
/ethereum/contracts/bridge/L1ERC20Bridge.sol::82 => bytes memory create2Input = abi.encode(address(this), l2ProxyTokenBytecodeHash, _governor);
/ethereum/contracts/bridge/L1ERC20Bridge.sol::168 => data = abi.encode(data1, data2, data3);
/ethereum/contracts/bridge/L1ERC20Bridge.sol::283 => bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));
/ethereum/contracts/bridge/L1EthBridge.sol::58 => bytes memory create2Input = abi.encode(address(this));
/ethereum/contracts/zksync/DiamondInit.sol::54 => s.storedBlockHashes[0] = keccak256(abi.encode(storedBlockZero));
/ethereum/contracts/zksync/facets/DiamondCut.sol::27 => s.diamondCutStorage.proposedDiamondCutHash = keccak256(abi.encode(_facetCuts, _initAddress));
/ethereum/contracts/zksync/facets/DiamondCut.sol::61 => keccak256(abi.encode(_diamondCut.facetCuts, _diamondCut.initAddress)),
/ethereum/contracts/zksync/facets/Executor.sol::85 => return keccak256(abi.encode(_previousBlock.blockHash, _newBlock.newStateRoot));
/ethereum/contracts/zksync/facets/Executor.sol::122 => chainedPriorityTxsHash = keccak256(abi.encode(chainedPriorityTxsHash, canonicalTxHash));
/ethereum/contracts/zksync/facets/Executor.sol::182 => concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));
/ethereum/contracts/zksync/facets/Executor.sol::359 => return keccak256(abi.encode(passThroughDataHash, metadataHash, auxiliaryOutputHash));
/ethereum/contracts/zksync/facets/Executor.sol::381 => return abi.encode(_block.l2LogsTreeRoot, l2ToL1LogsHash, initialStorageChangesHash, repeatedStorageChangesHash);
/ethereum/contracts/zksync/facets/Executor.sol::386 => return keccak256(abi.encode(_storedBlockInfo));
/ethereum/contracts/zksync/facets/Mailbox.sol::163 => canonicalTxHash = keccak256(abi.encode(transaction));
/ethereum/contracts/zksync/libraries/Merkle.sol::30 => currentHash = keccak256(abi.encode(currentHash, _path[i]));
/ethereum/contracts/zksync/libraries/Merkle.sol::32 => currentHash = keccak256(abi.encode(_path[i], currentHash));
/zksync/contracts/bridge/L2ERC20Bridge.sol::114 => bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));
```

## [G05] Don't compare boolean expressions to boolean literals

`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

#### Instances:
```
/ethereum/contracts/zksync/facets/Executor.sol::259 => // #if DUMMY_VERIFIER == false
```

## [G06] Functions with access control cheaper if payable

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are `CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

#### Instances:
```
/ethereum/contracts/common/AllowList.sol::57 => function setPublicAccess(address _target, bool _enable) external onlyOwner {
/ethereum/contracts/common/AllowList.sol::65 => function setBatchPublicAccess(address[] calldata _targets, bool[] calldata _enables) external onlyOwner {
/ethereum/contracts/common/AllowList.sol::94 => ) external onlyOwner {
/ethereum/contracts/common/AllowList.sol::118 => ) external onlyOwner {
/ethereum/contracts/common/AllowList.sol::140 => function setPendingOwner(address _newPendingOwner) external onlyOwner {
/ethereum/contracts/zksync/facets/DiamondCut.sol::22 => function proposeDiamondCut(Diamond.FacetCut[] calldata _facetCuts, address _initAddress) external onlyGovernor {
/ethereum/contracts/zksync/facets/DiamondCut.sol::35 => function cancelDiamondCutProposal() external onlyGovernor {
/ethereum/contracts/zksync/facets/DiamondCut.sol::46 => function executeDiamondCutProposal(Diamond.DiamondCutData calldata _diamondCut) external onlyGovernor {
/ethereum/contracts/zksync/facets/DiamondCut.sol::78 => function emergencyFreezeDiamond() external onlyGovernor {
/ethereum/contracts/zksync/facets/DiamondCut.sol::91 => function unfreezeDiamond() external onlyGovernor {
/ethereum/contracts/zksync/facets/Executor.sol::208 => function executeBlocks(StoredBlockInfo[] calldata _blocksData) external nonReentrant onlyValidator {
/ethereum/contracts/zksync/facets/Executor.sol::225 => ) external nonReentrant onlyValidator {
/ethereum/contracts/zksync/facets/Executor.sol::336 => function revertBlocks(uint256 _newLastBlock) external nonReentrant onlyValidator {
/ethereum/contracts/zksync/facets/Governance.sol::15 => function setPendingGovernor(address _newPendingGovernor) external onlyGovernor {
/ethereum/contracts/zksync/facets/Governance.sol::45 => function setValidator(address _validator, bool _active) external onlyGovernor {
/ethereum/contracts/zksync/facets/Governance.sol::54 => function setL2BootloaderBytecodeHash(bytes32 _l2BootloaderBytecodeHash) external onlyGovernor {
/ethereum/contracts/zksync/facets/Governance.sol::69 => function setL2DefaultAccountBytecodeHash(bytes32 _l2DefaultAccountBytecodeHash) external onlyGovernor {
/ethereum/contracts/zksync/facets/Governance.sol::84 => function setPorterAvailability(bool _zkPorterIsAvailable) external onlyGovernor {
/ethereum/contracts/zksync/facets/Governance.sol::94 => function setVerifier(Verifier _newVerifier) external onlyGovernor {
/ethereum/contracts/zksync/facets/Governance.sol::104 => function setVerifierParams(VerifierParams calldata _newVerifierParams) external onlyGovernor {
/zksync/contracts/bridge/L2StandardERC20.sol::102 => function bridgeMint(address _to, uint256 _amount) external override onlyBridge {
/zksync/contracts/bridge/L2StandardERC20.sol::109 => function bridgeBurn(address _from, uint256 _amount) external override onlyBridge {
```
## [G07] Use assembly to check for address(0)

Use assembly to check for address(0)\Saves 6 gas per instance if using assembly to check for address(0)\
			assembly {\
			 if iszero(_addr) {\
			  mstore(0x00, "zero address")\
			  revert(0x00, 0x20)\
			 }\
			}

#### Instances:
```
/ethereum/contracts/bridge/L1EthBridge.sol::33 => address constant CONVENTIONAL_ETH_ADDRESS = address(0);
/ethereum/contracts/common/AllowList.sol::33 => require(_owner != address(0), "kq");
/ethereum/contracts/common/AllowList.sol::161 => emit NewPendingOwner(newOwner, address(0));
/ethereum/contracts/zksync/DiamondProxy.sol::29 => require(facetAddress != address(0), "F"); // Proxy has no facet for this selector
/ethereum/contracts/zksync/facets/Getters.sol::148 => require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");
/ethereum/contracts/zksync/facets/Governance.sol::37 => emit NewPendingGovernor(pendingGovernor, address(0));
/ethereum/contracts/zksync/libraries/Diamond.sol::126 => require(_facet != address(0), "G"); // facet with zero address cannot be added
/ethereum/contracts/zksync/libraries/Diamond.sol::135 => require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists
/ethereum/contracts/zksync/libraries/Diamond.sol::150 => require(_facet != address(0), "K"); // cannot replace facet with zero address
/ethereum/contracts/zksync/libraries/Diamond.sol::156 => require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address
/ethereum/contracts/zksync/libraries/Diamond.sol::170 => require(_facet == address(0), "a1"); // facet address must be zero
/ethereum/contracts/zksync/libraries/Diamond.sol::176 => require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet
/ethereum/contracts/zksync/libraries/Diamond.sol::278 => if (_init == address(0)) {
/zksync/contracts/bridge/L2ERC20Bridge.sol::61 => if (l1TokenAddress[expectedL2Token] == address(0)) {
/zksync/contracts/bridge/L2ERC20Bridge.sol::95 => require(l1Token != address(0), "yh");
/zksync/contracts/bridge/L2ETHBridge.sol::28 => address constant CONVENTIONAL_ETH_ADDRESS = address(0);
/zksync/contracts/bridge/L2StandardERC20.sol::44 => require(l1Address == address(0), "in5"); // Is already initialized
/zksync/contracts/bridge/L2StandardERC20.sol::45 => require(_l1Address != address(0), "in6"); // Should be non-zero address
```