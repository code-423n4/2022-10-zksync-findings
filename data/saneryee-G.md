# Gas Optimizations

|          | Issue                                                                                 | Instances |
| -------- | :------------------------------------------------------------------------------------ | :-------: |
| [G-001]  | Functions guaranteed to revert when called by normal users can be marked payable      |    19     |
| [G-002]  | Empty blocks should be removed or emit something                                      |     3     |
| [G-003]  | Use Assembly to check for address(0)                                                  |     3     |
| [G-004]  | Using storage instead of memory for structs/arrays saves gas                          |     4     |
| [G-005]  | internal function not called by the contract should be removed to save deployment gas |    16     |
| [NC-001] | Constants should be defined rather than using magic numbers                           |     9     |
| [L-001]  | Unused `reveive()/fallback()` function                                                |     1     |
| [L-002]  | `decimals()` not part of ERC20 standard                                               |     1     |

## [G-001] Functions guaranteed to revert when called by normal users can be marked payable

### Description:

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

### Findings:

Total:19

[AllowList.sol#L57](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L57)

```
57:    function setPublicAccess(address _target, bool _enable) external onlyOwner {
```

[AllowList.sol#L65](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L65)

```
65:    function setBatchPublicAccess(address[] calldata _targets, bool[] calldata _enables) external onlyOwner {
```

[AllowList.sol#L94](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L94)

```
94:    ) external onlyOwner {
```

[AllowList.sol#L118](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L118)

```
118:    ) external onlyOwner {
```

[AllowList.sol#L140](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L140)

```
140:    function setPendingOwner(address _newPendingOwner) external onlyOwner {
```

[DiamondCut.sol#L22](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L22)

```
22:    function proposeDiamondCut(Diamond.FacetCut[] calldata _facetCuts, address _initAddress) external onlyGovernor {
```

[DiamondCut.sol#L35](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L35)

```
35:    function cancelDiamondCutProposal() external onlyGovernor {
```

[DiamondCut.sol#L46](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L46)

```
46:    function executeDiamondCutProposal(Diamond.DiamondCutData calldata _diamondCut) external onlyGovernor {
```

[DiamondCut.sol#L78](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L78)

```
78:    function emergencyFreezeDiamond() external onlyGovernor {
```

[DiamondCut.sol#L91](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L91)

```
91:    function unfreezeDiamond() external onlyGovernor {
```

[Governance.sol#L15](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L15)

```
15:    function setPendingGovernor(address _newPendingGovernor) external onlyGovernor {
```

[Governance.sol#L45](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L45)

```
45:    function setValidator(address _validator, bool _active) external onlyGovernor {
```

[Governance.sol#L54](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L54)

```
54:    function setL2BootloaderBytecodeHash(bytes32 _l2BootloaderBytecodeHash) external onlyGovernor {
```

[Governance.sol#L69](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L69)

```
69:    function setL2DefaultAccountBytecodeHash(bytes32 _l2DefaultAccountBytecodeHash) external onlyGovernor {
```

[Governance.sol#L84](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L84)

```
84:    function setPorterAvailability(bool _zkPorterIsAvailable) external onlyGovernor {
```

[Governance.sol#L94](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L94)

```
94:    function setVerifier(Verifier _newVerifier) external onlyGovernor {
```

[Governance.sol#L104](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L104)

```
104:    function setVerifierParams(VerifierParams calldata _newVerifierParams) external onlyGovernor {
```

[L2StandardERC20.sol#L102](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L102)

```
102:    function bridgeMint(address _to, uint256 _amount) external override onlyBridge {
```

[L2StandardERC20.sol#L109](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L109)

```
109:    function bridgeBurn(address _from, uint256 _amount) external override onlyBridge {
```

## [G-002] Empty blocks should be removed or emit something

### Description:

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### Findings:

Total:3

[DiamondInit.sol#L15](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondInit.sol#L15)

```
15:    constructor() reentrancyGuardInitializer {}
```

[IZkSync.sol#L11](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IZkSync.sol#L11)

```
11:    interface IZkSync is IMailbox, IGovernance, IExecutor, IDiamondCut, IGetters {}
```

[L2StandardERC20.sol#L39](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L39)

```
39:    constructor() initializer {}
```

## [G-003] Use Assembly to check for address(0)

### Description:

Saves 6 gas per instance if using assemlby to check for address(0)

### Findings:

Total:3

[AllowList.sol#L33](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L33)

```
33:    require(_owner != address(0), "kq");
```

[Getters.sol#L148](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Getters.sol#L148)

```
148:    require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");
```

[L2ERC20Bridge.sol#L95](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L95)

```
95:    require(l1Token != address(0), "yh");
```

## [G-004] Using storage instead of memory for structs/arrays saves gas

### Description:

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Goldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read.

### Findings:

Total:4

[L1EthBridge.sol#L71](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L71)

```
71:    bytes[] memory factoryDeps = new bytes[](1);
```

[Executor.sol#L234](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L234)

```
234:    uint256[] memory proofPublicInput = new uint256[](committedBlocksLength);
```

[Diamond.sol#L90](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L90)

```
90:    FacetCut[] memory facetCuts = _diamondCut.facetCuts;
```

[Diamond.sol#L98](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L98)

```
98:    bytes4[] memory selectors = facetCuts[i].selectors;
```

## [G-005] internal function not called by the contract should be removed to save deployment gas

### Description:

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword.

### Findings:

Total:16

[L1ERC20Bridge.sol#L164](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L164)

```
164:    function _getERC20Getters(address _token) internal view returns (bytes memory data) {
```

[L1EthBridge.sol#L232](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L232)

```
232:    function _withdrawFunds(address _to, uint256 _amount) internal {
```

[AllowList.sol#L75](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L75)

```
75:    function _setPublicAccess(address _target, bool _enable) internal {
```

[Executor.sol#L177](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L177)

```
177:    function _collectOperationsFromPriorityQueue(uint256 _nPriorityOps) internal returns (bytes32 concatHash) {
```

[Executor.sol#L190](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L190)

```
190:    function _executeOneBlock(StoredBlockInfo memory _storedBlock, uint256 _executedBlockIdx) internal {
```

[Executor.sol#L296](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L296)

```
296:    function _verifyRecursivePartOfProof(uint256[] calldata _recurisiveAggregationInput) internal view returns (bool) {
```

[Executor.sol#L349](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L349)

```
349:    function _maxU256(uint256 a, uint256 b) internal pure returns (uint256) {
```

[Executor.sol#L354](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L354)

```
354:    function _createBlockCommitment(CommitBlockInfo calldata _newBlockData) internal view returns (bytes32) {
```

[Executor.sol#L362](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L362)

```
362:    function _blockPassThroughData(CommitBlockInfo calldata _block) internal pure returns (bytes memory) {
```

[Executor.sol#L372](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L372)

```
372:    function _blockMetaParameters() internal view returns (bytes memory) {
```

[Executor.sol#L376](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L376)

```
376:    function _blockAuxilaryOutput(CommitBlockInfo calldata _block) internal pure returns (bytes memory) {
```

[Executor.sol#L385](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L385)

```
385:    function _hashStoredBlockInfo(StoredBlockInfo memory _storedBlockInfo) internal pure returns (bytes32) {
```

[Mailbox.sol#L75](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol#L75)

```
75:    function _L2MessageToLog(L2Message calldata _message) internal pure returns (L2Log memory) {
```

[L2ERC20Bridge.sol#L73](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L73)

```
73:    function _deployL2Token(address _l1Token, bytes calldata _data) internal returns (address) {
```

[L2ERC20Bridge.sol#L122](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L122)

```
122:    function _getCreate2Salt(address _l1Token) internal pure returns (bytes32 salt) {
```

[L2ETHBridge.sol#L74](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol#L74)

```
74:    function _getL1WithdrawMessage(address _to, uint256 _amount) internal pure returns (bytes memory) {
```

## [NC-001] Constants should be defined rather than using magic numbers

### Description:

It is bad practice to use numbers directly in code without explanation.

### Findings:

Total:9

[L2ContractHelper.sol#L55](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol#L55)

```
55:    hashedBytecode = sha256(_bytecode) & 0x00000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
```

[Executor.sol#L310](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L310)

```
310:    0x198e9393920d483a7260bfb731fb5d25f1aa493335a9e71297e485b7aef312c2,
```

[Executor.sol#L311](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L311)

```
311:    0x1800deef121f1e76426a00665e5c4479674322d4f75edadd46debd5cd992f6ed
```

[Executor.sol#L314](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L314)

```
314:    0x090689d0585ff075ec9e99ad690c3395bc4b313370b38ef355acdadcd122975b,
```

[Executor.sol#L315](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L315)

```
315:    0x12c85ea5db8c6deb4aab71808dcb408fe3d1e7690c43d37b4ce6cc0166fa7daa
```

[Executor.sol#L320](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L320)

```
320:    0x260e01b251f6f1c7e7ff4e580791dee8ea51d87a358e038b4efe30fac09383c1,
```

[Executor.sol#L321](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L321)

```
321:    0x0118c4d5b837bcc2bc89b5b398b5974e9f5944073b32078b7e231fec938883b0
```

[Executor.sol#L324](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L324)

```
324:    0x04fc6369f7110fe3d25156c1bb9a72859cf2a04641f99ba4ee413c80da6a5fe4,
```

[Executor.sol#L325](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L325)

```
325:    0x22febda3c0c0632a56475b4214e5615e11e6dd3f96e6cea2854a87d4dacc5e55
```

## [L-001] Unused `reveive()/fallback()` function

### Description:

The following contracts define a payable receive function but have no way of withdrawing or utilizing the sent Ether, resulting in Ether being locked in the contracts. If the intention is to use the Ether that functionality should be added to the function, otherwise it should revert.

### Findings:

Total:1

[DiamondProxy.sol#L19](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondProxy.sol#L19)

```
19:    fallback() external payable {
```

## [L-002] `decimals()` not part of ERC20 standard

### Description:

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.

### Findings:

Total:1

[L2StandardERC20.sol#L126](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L126)

```
126:    function decimals() public view override returns (uint8) {
```

