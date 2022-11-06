# QA Report
## L-1: should check the address(0) `L1ERC20Bridge#deposit`

In the zkSync, the address of ETH is set like this.

And `L1EthBridge#deposit` checks the `tokenAddress` is `CONVENTIONAL_ETH_ADDRESS`

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L32-L33](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L32-L33)

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L88-L93](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L88-L93)

```solidity
// L1EthBridge.sol

/// @dev Ether native coin has no real address on L1, so a conventional zero address is used.
address constant CONVENTIONAL_ETH_ADDRESS = address(0);

function deposit(
        address _l2Receiver,
        address _l1Token,//address(0)
        uint256 _amount
    ) external payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 txHash) {
        require(_l1Token == CONVENTIONAL_ETH_ADDRESS, "bx");
				/* ~~~ */
```

The `L1ERC20Bridge#deposit` has no checks  the `tokenAddress` is not `CONVENTIONAL_ETH_ADDRESS`
[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L111-L117](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L111-L117)

```solidity
// L1ERC20Bridge.sol
function deposit(
      address _l2Receiver,
      address _l1Token,
      uint256 _amount
  ) external payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 txHash) {
			// @audit-info _depositFunds has safeTransferFrom() by OpenZppelin
      uint256 amount = _depositFunds(msg.sender, IERC20(_l1Token), _amount);
      require(amount > 0, "1T"); // empty deposit amount
```

The `safeTransferFrom()` by OpenZeppelin will return revert in this case.
However, you should prevent this issue like this.

```solidity
// L1ERC20Bridge.sol
function deposit(
      address _l2Receiver,
      address _l1Token,
      uint256 _amount
  ) external payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 txHash) {
      require(_l1Token != CONVENTIONAL_ETH_ADDRESS, "bx");
      uint256 amount = _depositFunds(msg.sender, IERC20(_l1Token), _amount);
      require(amount > 0, "1T"); // empty deposit amount
```

## L-2: Can be overflow

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/libraries/UncheckedMath.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/libraries/UncheckedMath.sol)

```solidity
library UncheckedMath {
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
}
```

The `unchecked` keyword can set the default overflow and underflow checks to off. This is used to optimize the gas cost but there are no overflow checks so, it happens to overflow silently.

Therefore, you should add checking to prevent overflow or add a comment.

e.g) “Be sure to check overflow before using this library”

## L-3: `require()` should be used instead of `assert()`

### 📝 Description

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction's available gas rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix

### 💡 Recommendation

You should change from `assert()` to `require()`

### 🔍 Findings:

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L16` [assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L16)

## L-4: should add checking return value by `readAddress()`

`L1EthBridge.sol`

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L214-L228](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L214-L228)

`L1ERC20Bridge.sol`

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L225-L257](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L225-L257)

```solidity
function _parseL2WithdrawalMessage(bytes memory _message)
    internal
    pure
    returns (address l1Receiver, uint256 amount)
{
		/* ~~~ */
    (l1Receiver, offset) = UnsafeBytes.readAddress(_message, offset);
    (amount, offset) = UnsafeBytes.readUint256(_message, offset);
}
```

`L1EthBridge.sol`

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L182-L211](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L182-L211)

`L1ERC20Bridge.sol`

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L260-L279](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L260-L279)

```solidity
function finalizeWithdrawal(
        uint256 _l2BlockNumber,
        uint256 _l2MessageIndex,
        uint16 _l2TxNumberInBlock,
        bytes calldata _message,
        bytes32[] calldata _merkleProof
    ) external override nonReentrant senderCanCallFunction(allowList) {
				/* ~~~ */
        (address l1Receiver, uint256 amount) = _parseL2WithdrawalMessage(_message);
				/* ~~~ */
        _withdrawFunds(l1Receiver, amount);

        emit WithdrawalFinalized(l1Receiver, CONVENTIONAL_ETH_ADDRESS, amount);
    }
```

### 📝 Description

If the return value of `_parseL2WithdrawalMessage()` is as follows, the amount can’t withdraw forever

`(address l1Receiver, uint256 amount) = (address(0), 100000)`

Therefore, you should add checking to prevent this case

### 💡 Recommendation

`L1EthBridge.sol`

```solidity
(address l1Receiver, uint256 amount) = _parseL2WithdrawalMessage(_message);
require(l1Receiver != address(0) && amount != 0);
```

`L1ERC20Bridge.sol`

```solidity
(address l1Receiver, address l1Token, uint256 amount) = _parseL2WithdrawalMessage(l2ToL1Message.data);
require(l1Receiver != address(0) && ll1Token != address(0) && amount != 0);
```

## N-1: Open Todos

### 📝 Description

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

### 💡 Recommendation

Delete TODO keyword

### 🔍 Findings:

`2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L28` [// TODO: change constant to the real root hash of empty Merkle tree (SMA-184)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L28)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol#L43` [PairingsBn254.Fr[1] state_polys_openings_at_z_omega; // TODO: not use array while there is only D_next](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol#L43)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol#L224` [require(proof.state_polys_openings_at_z_omega.length == 1); // TODO](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol#L224)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol#L304` [// require(vk.num_inputs > 0); // TODO](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol#L304)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol#L308` [// TODO we may use batched lagrange compputation](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol#L308)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol#L454` [t.mul_assign(vk.non_residues[i - 1]); // TODO add one into non-residues during codegen?](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol#L454)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol#L485` [// TODO](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol#L485)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/Verifier.sol#L132` [// require(serialized_proof.length == 44); TODO](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Verifier.sol#L132)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L94` [// TODO: estimate gas for L1 execute](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L94)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L127` [// TODO: Restore after stable priority op fee modeling. (SMA-1230)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L127)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L169` [layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L169)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L56` [/// TODO: The verifier integration is not finished yet, change the structure for compatibility later](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L56)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PairingsBn254.sol#L208` [// TODO](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PairingsBn254.sol#L208)

## N-2: No use of two-phase ownership transfers

### 📝 Description

Consider adding a two-phase transfer, where the current owner nominates the next owner, and the next owner has to call `accept*()` to become the new owner. This prevents passing the ownership to an account that is unable to use it.

### 💡 Recommendation

Consider implementing a two step process where the admin nominates an account and the nominated account needs to call an acceptOwnership() function for the transfer of admin to fully succeed. This ensures the nominated EOA account is a valid and active account.

### 🔍 Findings:

`2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L34` [owner = _owner;](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L34)

`2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L158` [owner = newOwner;](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L158)

## N-3: Use `string.concat()` or`bytes.concat()`

### 📝 Description

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)Solidity version 0.8.12 introduces `string.concat()`(vs `abi.encodePacked(<str>,<str>)`)

### 💡 Recommendation

Use concat instead of abi.encodePacked

### 🔍 Findings:

`2022-10-zksync/blob/main/ethereum/contracts/common/libraries/UnsafeBytes.sol#L7` [* @dev The library provides a set of functions that help read data from an "abi.encodePacked" byte array.](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/libraries/UnsafeBytes.sol#L7)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L283` [abi.encodePacked(](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L283)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L364` [abi.encodePacked(](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L364)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L373` [return abi.encodePacked(s.zkPorterIsAvailable, s.l2BootloaderBytecodeHash, s.l2DefaultAccountBytecodeHash);](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L373)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L59` [abi.encodePacked(_log.l2ShardId, _log.isService, _log.txNumberInBlock, _log.sender, _log.key, _log.value)](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L59)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/TranscriptLib.sol#L29` [self.state_0 = keccak256(abi.encodePacked(DST_0, old_state_0, self.state_1, value));](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/TranscriptLib.sol#L29)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/TranscriptLib.sol#L30` [self.state_1 = keccak256(abi.encodePacked(DST_1, old_state_0, self.state_1, value));](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/TranscriptLib.sol#L30)

`2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/TranscriptLib.sol#L43` [bytes32 query = keccak256(abi.encodePacked(DST_CHALLENGE, self.state_0, self.state_1, self.challenge_counter));](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/TranscriptLib.sol#L43)

`2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L109` [return abi.encodePacked(IL1Bridge.finalizeWithdrawal.selector, _to, _l1Token, _amount);](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L109)

`2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L75` [return abi.encodePacked(IL1Bridge.finalizeWithdrawal.selector, _to, _amount);](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L75)

## N-4: Empty Revert

If returns revert, you should add comment to makes failed points clear

[https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L116](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L116)

[https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L122](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L122)

[https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L128](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L128)

## N-5: 404 Links

[https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/ReentrancyGuard.sol#L19](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/ReentrancyGuard.sol#L19)
This is invalid link
https://blog.openzeppelin.com/reentrancy-after-istanbul/[Reentrancy After Istanbul]

This is valid link
[https://blog.openzeppelin.com/reentrancy-after-istanbul/](https://blog.openzeppelin.com/reentrancy-after-istanbul/)

You should change as follows

```solidity
// before
// * https://blog.openzeppelin.com/reentrancy-after-istanbul/[Reentrancy After Istanbul].

// after
// * https://blog.openzeppelin.com/reentrancy-after-istanbul/  [Reentrancy After Istanbul].
```