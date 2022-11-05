### G01: custom error save more gas
#### problem
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information, as explained https://blog.soliditylang.org/2021/04/21/custom-errors/. 
Custom errors are defined using the error statement.
#### prof
bridge/L1ERC20Bridge.sol, 77, b'        require(_factoryDeps.length == 2, "mk");'
bridge/L1ERC20Bridge.sol, 117, b'        require(amount > 0, "1T"); // empty deposit amount'
bridge/L1ERC20Bridge.sol, 207, b'        require(success, "yn");'
bridge/L1ERC20Bridge.sol, 210, b'        require(amount > 0, "y1");'
bridge/L1ERC20Bridge.sol, 232, b'        require(!isWithdrawalFinalized[_l2BlockNumber][_l2MessageIndex], "pw");'
bridge/L1ERC20Bridge.sol, 249, b'            require(success, "nq");'
bridge/L1ERC20Bridge.sol, 271, b'        require(_l2ToL1message.length == 76, "kk");'
bridge/L1ERC20Bridge.sol, 274, b'        require(bytes4(functionSignature) == this.finalizeWithdrawal.selector, "nt");'
bridge/L1EthBridge.sol, 93, b'        require(_l1Token == CONVENTIONAL_ETH_ADDRESS, "bx");'
bridge/L1EthBridge.sol, 141, b'        require(_l1Token == CONVENTIONAL_ETH_ADDRESS, "sj");'
bridge/L1EthBridge.sol, 166, b'        require(success, "ju");'
bridge/L1EthBridge.sol, 189, b'        require(!isWithdrawalFinalized[_l2BlockNumber][_l2MessageIndex], "jj");'
bridge/L1EthBridge.sol, 205, b'        require(success, "rj");'
common/ReentrancyGuard.sol, 55, b'        require(lockSlotOldValue == 0, "1B");'
common/AllowList.sol, 33, b'        require(_owner != address(0), "kq");'
common/AllowList.sol, 67, b'        require(targetsLength == _enables.length, "yg"); // The size of arrays should be equal'
common/AllowList.sol, 101, b'        require(\n            callersLength == _targets.length &&\n                callersLength == _functionSigs.length &&\n                callersLength == _enables.length,\n            "yw"\n        ); // The size of arrays should be equal'
common/AllowList.sol, 155, b'        require(msg.sender == newOwner, "n0"); // Only proposed by current owner address can claim the owner rights'
common/L2ContractHelper.sol, 50, b'        require(_bytecode.length % 32 == 0, "po");'
common/L2ContractHelper.sol, 53, b'        require(bytecodeLenInWords < 2**16, "pp"); // bytecode length must be less than 2^16 words'
common/L2ContractHelper.sol, 54, b'        require(bytecodeLenInWords % 2 == 1, "pr"); // bytecode length in words must be odd'
common/L2ContractHelper.sol, 65, b'        require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash'
common/L2ContractHelper.sol, 67, b'        require(bytecodeLen(_bytecodeHash) % 2 == 1, "uy"); // Code length in words must be odd'
zksync/facets/Governance.sol, 30, b'        require(msg.sender == pendingGovernor, "n4"); // Only proposed by current governor address can claim the governor rights'
zksync/facets/Getters.sol, 148, b'        require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");'
zksync/libraries/PairingsBn254.sol, 82, b'        require(x < q_mod, "x axis isn\'t valid");'
zksync/libraries/PairingsBn254.sol, 83, b'        require(y < q_mod, "y axis isn\'t valid");'
zksync/libraries/PairingsBn254.sol, 90, b'        require(lhs == rhs, "is not on curve");'
zksync/libraries/Diamond.sol, 100, b'            require(selectors.length > 0, "B"); // no functions for diamond cut'
zksync/libraries/Diamond.sol, 126, b'        require(_facet != address(0), "G"); // facet with zero address cannot be added'
zksync/libraries/Diamond.sol, 135, b'            require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists'
zksync/libraries/Diamond.sol, 150, b'        require(_facet != address(0), "K"); // cannot replace facet with zero address'
zksync/libraries/Diamond.sol, 156, b'            require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address'
zksync/libraries/Diamond.sol, 170, b'        require(_facet == address(0), "a1"); // facet address must be zero'
zksync/libraries/Diamond.sol, 176, b'            require(oldFacet.facetAddress != address(0), "a2"); // Can\'t delete a non-existent facet'
zksync/libraries/Diamond.sol, 214, b'            require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");'
zksync/libraries/Diamond.sol, 283, b'            require(success, "I"); // delegatecall failed'
zksync/libraries/Diamond.sol, 287, b'            require(data.length == 32, "lp");'
zksync/libraries/Diamond.sol, 288, b'            require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");'
zksync/libraries/Diamond.sol, 279, b'            require(_calldata.length == 0, "H"); // Non-empty calldata for zero address'
zksync/libraries/Merkle.sol, 23, b'        require(pathLength > 0, "xc");'
zksync/libraries/Merkle.sol, 24, b'        require(pathLength < 256, "bt");'
zksync/libraries/Merkle.sol, 25, b'        require(_index < 2**pathLength, "pz");'
zksync/libraries/PriorityQueue.sol, 64, b'        require(!_queue.isEmpty(), "D"); // priority queue is empty'
zksync/libraries/PriorityQueue.sol, 72, b'        require(!_queue.isEmpty(), "s"); // priority queue is empty'
bridge/L2ETHBridge.sol, 49, b'        require(msg.sender == l1Bridge, "ni");'
bridge/L2ETHBridge.sol, 64, b'        require(_l2Token == CONVENTIONAL_ETH_ADDRESS, "zn");'
bridge/L2StandardERC20.sol, 44, b'        require(l1Address == address(0), "in5"); // Is already initialized'
bridge/L2StandardERC20.sol, 45, b'        require(_l1Address != address(0), "in6"); // Should be non-zero address'
bridge/L2ERC20Bridge.sol, 58, b'        require(msg.sender == l1Bridge, "mq");'
bridge/L2ERC20Bridge.sol, 63, b'            require(deployedToken == expectedL2Token, "mt");'
bridge/L2ERC20Bridge.sol, 95, b'        require(l1Token != address(0), "yh");'

### G02: COMPARISONS WITH ZERO FOR UNSIGNED INTEGERS
#### problem
0 is less gas efficient than !0 if you enable the optimizer at 10k AND you’re in a require statement. Detailed explanation with the opcodes https://twitter.com/gzeon/status/1485428085885640706
#### prof
bridge/L1ERC20Bridge.sol, 117, b'        require(amount > 0, "1T"); // empty deposit amount'
bridge/L1ERC20Bridge.sol, 210, b'        require(amount > 0, "y1");'
zksync/libraries/Diamond.sol, 100, b'            require(selectors.length > 0, "B"); // no functions for diamond cut'
zksync/libraries/Merkle.sol, 23, b'        require(pathLength > 0, "xc");'

### G03: USING BOOLS FOR STORAGE INCURS OVERHEAD
#### problem
// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.
#### prof
bridge/L1ERC20Bridge.sol, 40, b'    mapping(uint256 => mapping(uint256 => bool)) public isWithdrawalFinalized;'
bridge/L1EthBridge.sol, 37, b'    mapping(uint256 => mapping(uint256 => bool)) public isWithdrawalFinalized;'
common/AllowList.sol, 26, b'    mapping(address => bool) public isAccessPublic;'
common/AllowList.sol, 30, b'    mapping(address => mapping(address => mapping(bytes4 => bool))) public hasSpecialAccessToCall;'

### G04: resign the default value to the variables.
#### problem
 resign the default value to the variables will cost more gas.
#### prof
common/AllowList.sol, 69, b'        for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {'
common/AllowList.sol, 103, b'        for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {'
zksync/Verifier.sol, 134, b'        for (uint256 i = 0; i < public_inputs.length; i = i.uncheckedInc()) {'
zksync/Verifier.sol, 139, b'        for (uint256 i = 0; i < STATE_WIDTH; i = i.uncheckedInc()) {'
zksync/Verifier.sol, 164, b'        for (uint256 i = 0; i < proof.quotient_poly_parts_commitments.length; i = i.uncheckedInc()) {'
zksync/Verifier.sol, 172, b'        for (uint256 i = 0; i < proof.state_polys_openings_at_z.length; i = i.uncheckedInc()) {'
zksync/Verifier.sol, 178, b'        for (uint256 i = 0; i < proof.state_polys_openings_at_z_omega.length; i = i.uncheckedInc()) {'
zksync/Verifier.sol, 183, b'        for (uint256 i = 0; i < proof.gate_selectors_openings_at_z.length; i = i.uncheckedInc()) {'
zksync/Verifier.sol, 188, b'        for (uint256 i = 0; i < proof.copy_permutation_polys_openings_at_z.length; i = i.uncheckedInc()) {'
zksync/Plonk4VerifierWithAccessToDNext.sol, 144, b'        for (uint256 i = 0; i < vk.num_inputs; i = i.uncheckedInc()) {'
zksync/Plonk4VerifierWithAccessToDNext.sol, 148, b'        for (uint256 i = 0; i < STATE_WIDTH; i = i.uncheckedInc()) {'
zksync/Plonk4VerifierWithAccessToDNext.sol, 164, b'        for (uint256 i = 0; i < proof.quotient_poly_parts_commitments.length; i = i.uncheckedInc()) {'
zksync/Plonk4VerifierWithAccessToDNext.sol, 171, b'        for (uint256 i = 0; i < proof.state_polys_openings_at_z.length; i = i.uncheckedInc()) {'
zksync/Plonk4VerifierWithAccessToDNext.sol, 175, b'        for (uint256 i = 0; i < proof.state_polys_openings_at_z_omega.length; i = i.uncheckedInc()) {'
zksync/Plonk4VerifierWithAccessToDNext.sol, 178, b'        for (uint256 i = 0; i < proof.gate_selectors_openings_at_z.length; i = i.uncheckedInc()) {'
zksync/Plonk4VerifierWithAccessToDNext.sol, 181, b'        for (uint256 i = 0; i < proof.copy_permutation_polys_openings_at_z.length; i = i.uncheckedInc()) {'
zksync/Plonk4VerifierWithAccessToDNext.sol, 301, b'        for (uint256 i = 0; i < lagrange_poly_numbers.length; i = i.uncheckedInc()) {'
zksync/Plonk4VerifierWithAccessToDNext.sol, 307, b'        for (uint256 i = 0; i < vk.num_inputs; i = i.uncheckedInc()) {'
zksync/Plonk4VerifierWithAccessToDNext.sol, 325, b'        for (uint256 i = 0; i < proof.copy_permutation_polys_openings_at_z.length; i = i.uncheckedInc()) {'
zksync/Plonk4VerifierWithAccessToDNext.sol, 449, b'        for (uint256 i = 0; i < proof.state_polys_openings_at_z.length; ) {'
zksync/Plonk4VerifierWithAccessToDNext.sol, 473, b'        for (uint256 i = 0; i < STATE_WIDTH - 1; i = i.uncheckedInc()) {'
zksync/Plonk4VerifierWithAccessToDNext.sol, 560, b'        for (uint256 i = 0; i < STATE_WIDTH - 1; i = i.uncheckedInc()) {'
zksync/Plonk4VerifierWithAccessToDNext.sol, 615, b'        for (uint256 i = 0; i < STATE_WIDTH; i = i.uncheckedInc()) {'
zksync/Plonk4VerifierWithAccessToDNext.sol, 624, b'        for (uint256 i = 0; i < STATE_WIDTH - 1; i = i.uncheckedInc()) {'
zksync/facets/Getters.sol, 163, b'        for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {'
zksync/libraries/PairingsBn254.sol, 241, b'        for (uint256 i = 0; i < elements; ) {'
zksync/libraries/Diamond.sol, 94, b'        for (uint256 i = 0; i < facetCutsLength; ++i) {'
zksync/libraries/Diamond.sol, 132, b'        for (uint256 i = 0; i < selectorsLength; ++i) {'
zksync/libraries/Diamond.sol, 153, b'        for (uint256 i = 0; i < selectorsLength; ++i) {'
zksync/libraries/Diamond.sol, 173, b'        for (uint256 i = 0; i < selectorsLength; ++i) {'
zksync/libraries/Merkle.sol, 28, b'        for (uint256 i = 0; i < pathLength; i = i.uncheckedInc()) {'

### G05: ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS
#### problem
The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop
#### prof
zksync/Plonk4VerifierWithAccessToDNext.sol, 462, b'                ++i;'
zksync/libraries/PairingsBn254.sol, 249, b'                ++i;'
zksync/libraries/Diamond.sol, 94, b'        for (uint256 i = 0; i < facetCutsLength; ++i) {'
zksync/libraries/Diamond.sol, 132, b'        for (uint256 i = 0; i < selectorsLength; ++i) {'
zksync/libraries/Diamond.sol, 153, b'        for (uint256 i = 0; i < selectorsLength; ++i) {'
zksync/libraries/Diamond.sol, 173, b'        for (uint256 i = 0; i < selectorsLength; ++i) {'

### G06：ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()
#### prof
bridge/L1ERC20Bridge.sol, 82, b'        bytes memory create2Input = abi.encode(address(this), l2ProxyTokenBytecodeHash, _governor);'
bridge/L1ERC20Bridge.sol, 283, b'        bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));'
bridge/L1EthBridge.sol, 58, b'        bytes memory create2Input = abi.encode(address(this));'

### G07: Splitting require() statements that use && saves gas
#### problem
See this issue(https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper
#### prof:
common/AllowList.sol, 101, b'        require(\n            callersLength == _targets.length &&\n                callersLength == _functionSigs.length &&\n                callersLength == _enables.length,\n            "yw"\n        ); // The size of arrays should be equal'
common/L2ContractHelper.sol, 65, b'        require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash'

### G08: FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE
#### problem
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost
#### prof
common/AllowList.sol, 59, b'    function setPublicAccess(address _target, bool _enable) external onlyOwner '
common/AllowList.sol, 59, b'    function setPublicAccess(address _target, bool _enable) external onlyOwner '
common/AllowList.sol, 72, b'    function setBatchPublicAccess(address[] calldata _targets, bool[] calldata _enables) external onlyOwner '
common/AllowList.sol, 72, b'    function setBatchPublicAccess(address[] calldata _targets, bool[] calldata _enables) external onlyOwner '
common/AllowList.sol, 106, b'    function setBatchPermissionToCall(\n        address[] calldata _callers,\n        address[] calldata _targets,\n        bytes4[] calldata _functionSigs,\n        bool[] calldata _enables\n    ) external onlyOwner '
common/AllowList.sol, 106, b'    function setBatchPermissionToCall(\n        address[] calldata _callers,\n        address[] calldata _targets,\n        bytes4[] calldata _functionSigs,\n        bool[] calldata _enables\n    ) external onlyOwner '
common/AllowList.sol, 120, b'    function setPermissionToCall(\n        address _caller,\n        address _target,\n        bytes4 _functionSig,\n        bool _enable\n    ) external onlyOwner '
common/AllowList.sol, 120, b'    function setPermissionToCall(\n        address _caller,\n        address _target,\n        bytes4 _functionSig,\n        bool _enable\n    ) external onlyOwner '
common/AllowList.sol, 150, b'    function setPendingOwner(address _newPendingOwner) external onlyOwner '
common/AllowList.sol, 150, b'    function setPendingOwner(address _newPendingOwner) external onlyOwner '
common/AllowList.sol, 164, b'    function acceptOwner() external '
common/AllowList.sol, 164, b'    function acceptOwner() external '

### G09: USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD
#### problem
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
#### prof
bridge/L1ERC20Bridge.sol, 77, b'        require(_factoryDeps.length == 2, "mk");'
bridge/L1ERC20Bridge.sol, 81, b'        bytes32 create2Salt = bytes32(0);'
bridge/L1ERC20Bridge.sol, 98, b'            0,'
bridge/L1ERC20Bridge.sol, 117, b'        require(amount > 0, "1T"); // empty deposit amount'
bridge/L1ERC20Bridge.sol, 167, b'        (, bytes memory data3) = _token.staticcall(abi.encodeCall(IERC20Metadata.decimals, ()));'
bridge/L1ERC20Bridge.sol, 198, b'        L2Log memory l2Log = L2Log({'
bridge/L1ERC20Bridge.sol, 199, b'            l2ShardId: 0,'
bridge/L1ERC20Bridge.sol, 201, b'            txNumberInBlock: _l2TxNumberInBlock,'
bridge/L1ERC20Bridge.sol, 204, b'            value: bytes32(0)'
bridge/L1ERC20Bridge.sol, 210, b'        require(amount > 0, "y1");'
bridge/L1ERC20Bridge.sol, 234, b'        L2Message memory l2ToL1Message = L2Message({'
bridge/L1ERC20Bridge.sol, 235, b'            txNumberInBlock: _l2TxNumberInBlock,'
bridge/L1ERC20Bridge.sol, 271, b'        require(_l2ToL1message.length == 76, "kk");'
...
zksync/libraries/Diamond.sol, 279, b'            require(_calldata.length == 0, "H"); // Non-empty calldata for zero address'
zksync/libraries/Merkle.sol, 23, b'        require(pathLength > 0, "xc");'
zksync/libraries/Merkle.sol, 25, b'        require(_index < 2**pathLength, "pz");'
zksync/libraries/Merkle.sol, 29, b'            if (_index % 2 == 0) {'
zksync/libraries/Merkle.sol, 29, b'            if (_index % 2 == 0) {'
zksync/libraries/Merkle.sol, 28, b'        for (uint256 i = 0; i < pathLength; i = i.uncheckedInc()) {'
zksync/libraries/PriorityQueue.sol, 35, b'        return _queue.head;'
zksync/libraries/PriorityQueue.sol, 40, b'        return _queue.tail;'
zksync/libraries/PriorityQueue.sol, 45, b'        return uint256(_queue.tail - _queue.head);'
zksync/libraries/PriorityQueue.sol, 45, b'        return uint256(_queue.tail - _queue.head);'
zksync/libraries/PriorityQueue.sol, 50, b'        return _queue.tail == _queue.head;'
zksync/libraries/PriorityQueue.sol, 50, b'        return _queue.tail == _queue.head;'

### G10：ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()
#### prof
bridge/L2ERC20Bridge.sol, 114, b'        bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));'