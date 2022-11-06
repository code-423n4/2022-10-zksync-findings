# Gas Optimization

## G-1: Use assembly to ADD

### 📝 Description

Use Optimization 200 and I used remix

Input value is as follows

`_number = 1`

| Execution cost(gas) | Deploy Cost | Function Cost |
| --- | --- | --- |
| Gas1 | 98757 | 21531 |
| Gas2 | 90143 | 21421 |
| Gap | - 8614 | -110 |

You can save about 8000 gas per instance in deploying contracrt.
You can save about 110 gas per instance if using assembly to execute the function.

```solidity
//SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.0;

contract Gas1 {
    function uncheckedInc1(uint256 _number) public pure returns (uint256) {
        unchecked {
            _number + 1;
        }
        return _number + 1;
    }
}

contract Gas2 {
    function uncheckedInc2(uint256 _number) public pure returns (uint256) {
        unchecked {
            assembly {
                let result := add(_number,1)
                  mstore(0x0, result)
                  return(0x0, 32)
            }
        }
    }
}
```

### 💡 Recommendation

Use the above code when adding values

### 🔍 Findings:

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/libraries/UncheckedMath.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/libraries/UncheckedMath.sol)

```solidity
function uncheckedInc(uint256 _number) internal pure returns (uint256) {
        unchecked {
            return _number + 1;
        }
    }

    function uncheckedAdd(uint256 _lhs, uint256 _rhs) internal pure returns (uint256) {
        unchecked {
            return _lhs + _rhs;
        }
    }
```

## G-2: Use assembly to hash

### 📝 Description

Use Optimization 200 and I used remix

Input value is as follows

`(a,b) = (111,111)`

| Execution cost(gas) | Deploy Cost | Function Cost |
| --- | --- | --- |
| Gas1 | 92955 | 21657 |
| Gas2 | 87101 | 21575 |
| Gap | - 5854 | - 82 |

See [this issue](https://github.com/code-423n4/2022-06-putty-findings/issues/59)
You can save about 6000 gas per instance in deploying contract.
You can save about 80 gas per instance if using assembly to execute the function.

### 💡 Recommendation

Use the above code when hasing values

### 🔍 Findings:

`2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L93` [keccak256(create2Input)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L93)

`2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L283` [bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L283)

`2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L69` [keccak256(create2Input)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L69)

`2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L41` [bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L41)

`2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L82` [bytes32 data = keccak256(](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L82)

`2022-10-zksync/blob/main/ethereum/contracts/common/ReentrancyGuard.sol#L27` [uint256 private constant LOCK_FLAG_ADDRESS = 0x8e94fed44239eb2314ab7a406345e6c5a8f0ccedf3b600de3d004e672c33abf4; // keccak256("ReentrancyGuard") - 1;](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/ReentrancyGuard.sol#L27)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L19` [/// @dev Actually equal to the `keccak256(new bytes(L2_LOG_BYTES))`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L19)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#L54` [s.storedBlockHashes[0] = keccak256(abi.encode(storedBlockZero));](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#L54)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L27` [s.diamondCutStorage.proposedDiamondCutHash = keccak256(abi.encode(_facetCuts, _initAddress));](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L27)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L61` [keccak256(abi.encode(_diamondCut.facetCuts, _diamondCut.initAddress)),](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L61)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L85` [return keccak256(abi.encode(_previousBlock.blockHash, _newBlock.newStateRoot));](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L85)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L117` [require(keccak256(l2Messages[currentMessage]) == hashedMessage, "k2");](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L117)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L122` [chainedPriorityTxsHash = keccak256(abi.encode(chainedPriorityTxsHash, canonicalTxHash));](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L122)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L182` [concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L182)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L282` [keccak256(](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L282)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L355` [bytes32 passThroughDataHash = keccak256(_blockPassThroughData(_newBlockData));](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L355)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L356` [bytes32 metadataHash = keccak256(_blockMetaParameters());](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L356)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L357` [bytes32 auxiliaryOutputHash = keccak256(_blockAuxilaryOutput(_newBlockData));](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L357)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L359` [return keccak256(abi.encode(passThroughDataHash, metadataHash, auxiliaryOutputHash));](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L359)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L377` [bytes32 initialStorageChangesHash = keccak256(_block.initialStorageChanges);](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L377)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L378` [bytes32 repeatedStorageChangesHash = keccak256(_block.repeatedStorageChanges);](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L378)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L379` [bytes32 l2ToL1LogsHash = keccak256(_block.l2Logs);](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L379)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L386` [return keccak256(abi.encode(_storedBlockInfo));](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L386)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L58` [bytes32 hashedLog = keccak256(](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L58)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L83` [value: keccak256(_message.data)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L83)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L163` [canonicalTxHash = keccak256(abi.encode(transaction));](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L163)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IMailbox.sol#L91` [/// @param txHash keccak256 hash of encoded transaction representation](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IMailbox.sol#L91)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L11` [0x33774e659306e47509050e97cb651e731180a42d458212294d30751925c551a2; // keccak256("diamond.zksync.init") - 1](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L11)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L14` [bytes32 constant DIAMOND_STORAGE_POSITION = 0xc8fcad8db84d3cc18b4c41d551ea0ee66dd599cde068d998e57d5e09332c131b; // keccak256("diamond.standard.diamond.storage") - 1;](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L14)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol#L30` [currentHash = keccak256(abi.encode(currentHash, _path[i]));](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol#L30)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol#L32` [currentHash = keccak256(abi.encode(_path[i], currentHash));](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol#L32)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/TranscriptLib.sol#L29` [self.state_0 = keccak256(abi.encodePacked(DST_0, old_state_0, self.state_1, value));](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/TranscriptLib.sol#L29)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/TranscriptLib.sol#L30` [self.state_1 = keccak256(abi.encodePacked(DST_1, old_state_0, self.state_1, value));](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/TranscriptLib.sol#L30)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/TranscriptLib.sol#L43` [bytes32 query = keccak256(abi.encodePacked(DST_CHALLENGE, self.state_0, self.state_1, self.challenge_counter));](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/TranscriptLib.sol#L43)

`2022-10-zksync/blob/main/zksync/contracts/L2ContractHelper.sol#L24` [bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/L2ContractHelper.sol#L24)

`2022-10-zksync/blob/main/zksync/contracts/L2ContractHelper.sol#L37` [bytes32 data = keccak256(](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/L2ContractHelper.sol#L37)

`2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L114` [bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenFactory), ""));](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L114)

## G-3: Empty blocks should be removed or emit something

### 📝 Description

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### 💡 Recommendation

Empty blocks should be removed or emit something (The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### 🔍 Findings:

`2022-10-zksync/blob/main/ethereum/contracts/dev-contracts/test/DummyERC20NoTransferReturnValue.sol#L6` [function transfer(address recipient, uint256 amount) external {}](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/dev-contracts/test/DummyERC20NoTransferReturnValue.sol#L6)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#L15` [constructor() reentrancyGuardInitializer {}](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#L15)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IZkSync.sol#L11` [interface IZkSync is IMailbox, IGovernance, IExecutor, IDiamondCut, IGetters {}](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IZkSync.sol#L11)

`2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L39` [constructor() initializer {}](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L39)

## G-4: Splitting require() statements that use && saves gas

### 📝 Description

See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper

### 💡 Recommendation

You should change one require which has `&&` to two simple require() statements to save gas

### 🔍 Findings:

`2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L97` [callersLength == _targets.length &&](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L97)

`2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L98` [callersLength == _functionSigs.length &&](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L98)

`2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L65` [require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L65)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PairingsBn254.sol#L76` [if (x == 0 && y == 0) {](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PairingsBn254.sol#L76)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PairingsBn254.sol#L144` [if (p2.X == 0 && p2.Y == 0) {](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PairingsBn254.sol#L144)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PairingsBn254.sol#L149` [} else if (p1.X == 0 && p1.Y == 0) {](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PairingsBn254.sol#L149)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PairingsBn254.sol#L179` [if (p2.X == 0 && p2.Y == 0) {](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PairingsBn254.sol#L179)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PairingsBn254.sol#L184` [} else if (p1.X == 0 && p1.Y == 0) {](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PairingsBn254.sol#L184)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PairingsBn254.sol#L209` [if (p.X == 0 && p.Y == 1) {](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PairingsBn254.sol#L209)