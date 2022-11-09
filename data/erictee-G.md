### [G-01] Use assembly to check for zero address.


#### Impact
Save 6 gas when assembly is used to check for zero address.
e.g:
```
assembly {
            if iszero(_addr) {
                mstore(0x00, "zero address")
                revert(0x00, 0x20)
            }
```


#### Findings:
```
2022-10-zksync/ethereum/contracts/common/AllowList.sol:L33        require(_owner != address(0), "kq");

2022-10-zksync/ethereum/contracts/libraries/Diamond.sol:L126        require(_facet != address(0), "G"); // facet with zero address cannot be added

2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol:L135            require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists

2022-10-zksync/ethereum/contracts/libraries/Diamond.sol:L150        require(_facet != address(0), "K"); // cannot replace facet with zero address

2022-10-zksync/ethereum/contracts/libraries/Diamond.sol:L156            require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address

2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol:L170        require(_facet == address(0), "a1"); // facet address must be zero

2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol:L176            require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet

2022-10-zksync/ethereum/contracts/zksync/facets/Getters.sol:L148        require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");

2022-10-zksync/ethereum/contracts/zksync/DiamondProxy.sol:L29        require(facetAddress != address(0), "F"); // Proxy has no facet for this selector


```

### [G-02] Empty blocks should be removed or emit something


#### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.


#### Findings:
```

2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol:L39    constructor() initializer {}

2022-10-zksync/ethereum/contracts/zksync/DiamondInit.sol:L15    constructor() reentrancyGuardInitializer {}


```
        
### [G-03] Use a more recent version of solidity.


#### Impact
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining 
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads 
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings 
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.


#### Findings:
```
2022-10-zksync/zksync/contracts/L2ContractHelper.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/ExternalDecoder.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/L2ERC20Bridge.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/L2ETHBridge.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/interfaces/IL2Bridge.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/interfaces/IL2StandardToken.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/interfaces/IL2EthInitializable.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/interfaces/IL1Bridge.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/common/libraries/UncheckedMath.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/common/libraries/UnsafeBytes.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/common/interfaces/IAllowList.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/common/AllowListed.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/common/AllowList.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/common/ReentrancyGuard.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/libraries/Diamond.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/libraries/PriorityQueue.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/libraries/Merkle.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IMailbox.sol:L3 pragma solidity ^0.8;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IExecutor.sol:L3 pragma solidity ^0.8;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IGetters.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IGovernance.sol:L3 pragma solidity ^0.8;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IDiamondCut.sol:L3 pragma solidity ^0.8;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IZkSync.sol:L3 pragma solidity ^0.8;
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/Base.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/Getters.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/DiamondProxy.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/Config.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/Storage.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/DiamondInit.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/bridge/interfaces/IL2Bridge.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/bridge/interfaces/IL1Bridge.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol:L3 pragma solidity ^0.8.0;

```

### [G-04] Functions guaranteed to revert when called by normal users can be declared as payable.


#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.


#### Findings:
```
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol:L102    function bridgeMint(address _to, uint256 _amount) external override onlyBridge {
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol:L109    function bridgeBurn(address _from, uint256 _amount) external override onlyBridge {
2022-10-zksync/ethereum/contracts/common/AllowList.sol:L57    function setPublicAccess(address _target, bool _enable) external onlyOwner {
2022-10-zksync/ethereum/contracts/common/AllowList.sol:L65    function setBatchPublicAccess(address[] calldata _targets, bool[] calldata _enables) external onlyOwner {
2022-10-zksync/ethereum/contracts/common/AllowList.sol:L140    function setPendingOwner(address _newPendingOwner) external onlyOwner {
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol:L208    function executeBlocks(StoredBlockInfo[] calldata _blocksData) external nonReentrant onlyValidator {
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol:L336    function revertBlocks(uint256 _newLastBlock) external nonReentrant onlyValidator {
2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol:L15    function setPendingGovernor(address _newPendingGovernor) external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol:L45    function setValidator(address _validator, bool _active) external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol:L54    function setL2BootloaderBytecodeHash(bytes32 _l2BootloaderBytecodeHash) external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol:L69    function setL2DefaultAccountBytecodeHash(bytes32 _l2DefaultAccountBytecodeHash) external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol:L84    function setPorterAvailability(bool _zkPorterIsAvailable) external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol:L94    function setVerifier(Verifier _newVerifier) external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol:L104    function setVerifierParams(VerifierParams calldata _newVerifierParams) external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol:L22    function proposeDiamondCut(Diamond.FacetCut[] calldata _facetCuts, address _initAddress) external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol:L35    function cancelDiamondCutProposal() external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol:L46    function executeDiamondCutProposal(Diamond.DiamondCutData calldata _diamondCut) external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol:L78    function emergencyFreezeDiamond() external onlyGovernor {
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol:L91    function unfreezeDiamond() external onlyGovernor {

```

### [G-05] Splitting ```require()``` statements that use && saves gas.


#### Impact
Consider splitting the ```require()``` statements to save gas.


#### Findings:
```
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol:L65        require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash


2022-10-zksync/ethereum/contracts/common/AllowList.sol:L96            require(



```
