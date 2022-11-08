# 1. [G-1] Variables: "Constant" expressions are expressions, not constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

If the variable was immutable instead: the calculation would only be done once at deploy time (in the constructor), and then the result would be saved and read directly at runtime rather than being recalculated.

Instances include:

    File ethereum/contracts/common/L2ContractHelper.sol, line 28:      address constant BOOTLOADER_ADDRESS = address(SYSTEM_CONTRACTS_OFFSET + 0x01);
    File ethereum/contracts/common/L2ContractHelper.sol, line 30:      address constant DEPLOYER_SYSTEM_CONTRACT_ADDRESS = address(SYSTEM_CONTRACTS_OFFSET + 0x06);
    File ethereum/contracts/common/L2ContractHelper.sol, line 34:      address constant FORCE_DEPLOYER = address(SYSTEM_CONTRACTS_OFFSET + 0x07);
    File ethereum/contracts/common/L2ContractHelper.sol, line 36:      IL2Messenger constant L2_MESSENGER = IL2Messenger(address(SYSTEM_CONTRACTS_OFFSET + 0x08));
    File ethereum/contracts/common/L2ContractHelper.sol, line 38:      address constant VALUE_SIMULATOR_SYSTEM_CONTRACT_ADDRESS = address(SYSTEM_CONTRACTS_OFFSET + 0x09);
    File ethereum/contracts/common/L2ContractHelper.sol, line 41:      bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");

    File ethereum/contracts/zksync/Config.sol, line 12:      uint256 constant L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;
    File ethereum/contracts/zksync/Config.sol, line 23:      uint256 constant INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + 64 * 4896;
    File ethereum/contracts/zksync/Config.sol, line 26:      uint256 constant REPEATED_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + 40 * 7787;
    File ethereum/contracts/zksync/Config.sol, line 54, 60, 63, 69, 74, 80.

    File zksync/contracts/L2ContractHelper.sol, line 19:      address constant BOOTLOADER_ADDRESS = address(SYSTEM_CONTRACTS_OFFSET + 0x01);
    File zksync/contracts/L2ContractHelper.sol, line 20:      address constant VALUE_SIMULATOR_SYSTEM_CONTRACT_ADDRESS = address(SYSTEM_CONTRACTS_OFFSET + 0x09);
    File zksync/contracts/L2ContractHelper.sol, line 21:      IL2Messenger constant L2_MESSENGER = IL2Messenger(address(SYSTEM_CONTRACTS_OFFSET + 0x08));
    File zksync/contracts/L2ContractHelper.sol, line 24:      bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");

    File zksync/contracts/bridge/L2ETHBridge.sol, line 25:      IL2StandardToken constant ETH_TOKEN_SYSTEM_CONTRACT_ADDRESS = IL2StandardToken(address(0x800a));

Change these expressions from `constant` to `immutable` and implement the calculation in the constructor, or hardcode these values in the constants and add a comment to say how the value was calculated.

# 2. [G-2] Arithmetics: uncheck blocks for arithmetics operations that can't underflow/overflow

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn't possible, some gas can be saved by using an `unchecked` block.

I suggest wrapping with an `unchecked` block here:

    File ethereum/contracts/zksync/libraries/PriorityQueue.sol, line 59:     _queue.tail = tail + 1;
    File ethereum/contracts/zksync/libraries/PriorityQueue.sol, line 79:     _queue.head = head + 1;

    File ethereum/contracts/bridge/L1ERC20Bridge.sol, line 145:     return balanceAfter - balanceBefore;

    File ethereum/contracts/bridge/L1EthBridge.sol, line 96:     uint256 zkSyncFee = msg.value - _amount;

    File ethereum/contracts/common/L2ContractHelper.sol, line 50 - 55:    
                    require(_bytecode.length % 32 == 0, "po");

                    uint256 bytecodeLenInWords = _bytecode.length / 32;
                    require(bytecodeLenInWords < 2**16, "pp"); // bytecode length must be less than 2^16 words
                    require(bytecodeLenInWords % 2 == 1, "pr"); // bytecode length in words must be odd

# 3. [G-3] For loops: increments in for loop can be uncheck to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block

    File ethereum/contracts/zksync/libraries/Diamond.sol, line 94:     for (uint256 i = 0; i < facetCutsLength; ++i) {
    File ethereum/contracts/zksync/libraries/Diamond.sol, line 132:     for (uint256 i = 0; i < selectorsLength; ++i) {
    File ethereum/contracts/zksync/libraries/Diamond.sol, line 153:     for (uint256 i = 0; i < selectorsLength; ++i) {
    File ethereum/contracts/zksync/libraries/Diamond.sol, line 173:     for (uint256 i = 0; i < selectorsLength; ++i) {

The code would go from:

    for (uint256 i; i < numIterations; i++) { 
      ...
    }

to:

    for (uint256 i; i < numIterations;) { 
      ...
      unchecked { ++i; }  
    }

# 4. [G-4] Variables: Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas

I suggest using `memory` instead of `storage` when declare variable here:

    File ethereum/contracts/zksync/DiamondProxy.sol, line 20:
                 Diamond.DiamondStorage storage diamondStorage = Diamond.getDiamondStorage();

    File ethereum/contracts/zksync/facets/Getters.sol, line 128:       Diamond.DiamondStorage storage ds = Diamond.getDiamondStorage();
    File ethereum/contracts/zksync/facets/Getters.sol, line 134, 147, 158, 173, 185.

    File ethereum/contracts/zksync/libraries/Diamond.sol, line 124:       DiamondStorage storage ds = getDiamondStorage();
    File ethereum/contracts/zksync/libraries/Diamond.sol, line 148, 168.

# 5. [G-5] Parameter: If we are not modifying the passed parameter we should pass it as `calldata` because `calldata` is more gas efficient than `memory`.

I suggest using `calldata` instead of `memory` here:

    File ethereum/contracts/bridge/L1EthBridge.sol, line 214:           function _parseL2WithdrawalMessage(bytes memory _message)

    File ethereum/contracts/common/L2ContractHelper.sol, line 47:        function hashL2Bytecode(bytes memory _bytecode)
  
    File ethereum/contracts/common/libraries/UnsafeBytes.sol, line 18, 25, 32, 39.

    File ethereum/contracts/zksync/libraries/PriorityQueue.sol, line 54:     function pushBack(Queue storage _queue, PriorityOperation memory _operation)

    File zksync/contracts/ExternalDecoder.sol, line 10, 15.

