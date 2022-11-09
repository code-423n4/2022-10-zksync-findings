## Consider Uniform SPDX License

Not all source files specify the same license.

***

// SPDX-License-Identifier: MIT OR Apache-2.0
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Base.sol#L1

// SPDX-License-Identifier: MIT
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L1

## Solidity Version

Consider specifying the exact same version of solidity in all files.

***

pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L3

pragma solidity ^0.8;
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L3

## Open Todos

Finished code should not have any open todos.

***

    // TODO: change constant to the real root hash of empty Merkle tree (SMA-184)
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Config.sol#L28

    // TODO: estimate gas for L1 execute
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol#L94

    // TODO: Restore after stable priority op fee modeling. (SMA-1230)
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol#L127

    layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol#L169

    /// TODO: The verifier integration is not finished yet, change the structure for compatibility later
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L56

## Missing Indexed Fields in Events

Each event should use three indexed fields if there are three or more fields in the event.

***

event EmergencyDiamondCutApproved(
    address indexed _address,
    uint256 currentProposalId,
    uint256 securityCouncilEmergencyApprovals,
    bytes32 indexed proposedDiamondCutHash
);
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L30-L35

event NewPriorityRequest(
    uint256 txId,
    bytes32 txHash,
    uint64 expirationBlock,
    L2CanonicalTransaction transaction,
    bytes[] factoryDeps
);
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IMailbox.sol#L95-L101

event BridgeInitialization(address indexed l1Token, string name, string symbol, uint8 decimals);
https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L12

## Avoid Magic Numbers

Checking against magic numbers should be avoided.
Consider using a properly named constant instead.

***

require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondProxy.sol#L24

require(_l2ToL1message.length == 76, "kk");
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L271

require(_message.length == 56);
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L221

hashedBytecode = (hashedBytecode | bytes32(uint256(1 << 248)));
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol#L57

hashedBytecode = hashedBytecode | bytes32(bytecodeLenInWords << 224);
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol#L59

## Revert and Require Should Have Descriptive Reason Strings

require(expectedNumberOfLayer1Txs == _newBlock.numberOfLayer1Txs);
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L43

require(l2BlockTimestamp == _newBlock.timestamp);
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L45

require(_recurisiveAggregationInput.length == 4);
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L297

require(amount != 0);
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L145

require(_message.length == 56);
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L221

require(bytes4(functionSignature) == this.finalizeWithdrawal.selector);
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L224

require(callSuccess);
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L238

require(_l1Token == CONVENTIONAL_ETH_ADDRESS);
https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol#L50

require(msg.sender == l2Bridge);
https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L96

if (availableGetters.ignoreName) revert();
https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L116

if (availableGetters.ignoreSymbol) revert();
https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L122

if (availableGetters.ignoreDecimals) revert();
https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L128

## Avoid Overlong Lines

Limit line length to 164 characters.
Longer lines will need scrollbars in github and are hard to read anyways.

***

    /// @param _diamondCutHash The hash of the diamond cut that security council members want to approve. Needed to prevent unintentional approvals, including reorg attacks

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L104

    bytes32 constant DIAMOND_STORAGE_POSITION = 0xc8fcad8db84d3cc18b4c41d551ea0ee66dd599cde068d998e57d5e09332c131b; // keccak256("diamond.standard.diamond.storage") - 1;

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L14

    /// @param maxFeePerErg The absolute maximum sender willing to pay per unit of ergs to get the transaction included in a block. Analog to the EIP-1559 `maxFeePerGas` on an L1 transactions

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IMailbox.sol#L15

    /// @param maxPriorityFeePerErg The additional fee that is paid directly to the validator to incentivize them to include the transaction in a block. Analog to the EIP-1559 `maxPriorityFeePerGas` on an L1 transactions

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IMailbox.sol#L16

    /// @param paymaster The address of the EIP-4337 paymaster, that will pay fees for the transaction. `uint256` type for possible address format changes and maintaining backward compatibility

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IMailbox.sol#L17

