# Missing 0 address check when setting the governor owner and pending owner

### Line Of Code

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L74

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Governance.sol#L45

### Vulnerabilty detail and recommended fix

In L1ERC20Bridge.sol

```solidity
function initialize(
bytes[] calldata _factoryDeps,
address _l2TokenFactory,
address _governor
) external reentrancyGuardInitializer {
```

missing check require(_governor != address(0));

In Governance.sol,

```solidity
/// @notice Change validator status (active or not active)
/// @param _validator Validator address
/// @param _active Active flag
function setValidator(address _validator, bool _active) external onlyGovernor {
if (s.validators[_validator] != _active) {
s.validators[_validator] = _active;
emit ValidatorStatusUpdate(_validator, _active);
}
}
```

missing the check require(_validator != address(0));


# Owner can set pending owner / govnernor to the same admin address.

### Line Of Code

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Governance.sol#L15

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/AllowList.sol#L140

### Vulnerabilty detail and recommended fix

In AllowList.sol, the admin can set newPendingOrder to admin himself. 

the effect is that the contract becomes centralizated and there will be no pending admin capable of claiming the admin role
given there is no expiration time for newPendingOwner setting.

```solidity
function setPendingOwner(address _newPendingOwner) external onlyOwner {
// Save previous value into the stack to put it into the event later
address oldPendingOwner = pendingOwner;

if (oldPendingOwner != _newPendingOwner) {
	// Change pending owner
	pendingOwner = _newPendingOwner;

	emit NewPendingOwner(oldPendingOwner, _newPendingOwner);
}
}
```

We recommend add the check

```solidity
require(owner != _newPendingOwner);
```


In Governance.sol, the governor can set the newPendingGovernor to governor himself.

```solidity
function setPendingGovernor(address _newPendingGovernor) external onlyGovernor {
// Save previous value into the stack to put it into the event later
address oldPendingGovernor = s.pendingGovernor;

if (oldPendingGovernor != _newPendingGovernor) {
	// Change pending governor
	s.pendingGovernor = _newPendingGovernor;

	emit NewPendingGovernor(oldPendingGovernor, _newPendingGovernor);
}
}

```

We recommend add the check

```solidity
require(s.governor != _newPendingGovernor);
```


# Floating pragma solidity version

the contracts in scope use the floating progama 

```solidity
// SPDX-License-Identifier: MIT OR Apache-2.0

pragma solidity ^0.8.0;
```

Floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.


# lack of error message for require statement.

### Line Of Code

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L145

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L238

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/Verifier.sol#L231

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2StandardERC20.sol#L96

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L45

### Vulnerabilty detail and recommended fix

in L1ETHBridge.sol

```solidity
require(amount != 0);
```

```sollidity
/// @notice Transfer ether from the contract to the receiver
/// @dev Reverts only if the transfer call failed
function _withdrawFunds(address _to, uint256 _amount) internal {
bool callSuccess;
// Low-level assembly call, to avoid any memory copying (save gas)
assembly {
	callSuccess := call(gas(), _to, _amount, 0, 0, 0, 0)
}
require(callSuccess);
}
```

In Verifier.sol

```solidity
require(vk.num_inputs == public_inputs.length);
```

In Executor.sol

```solidity
require(l2BlockTimestamp == _newBlock.timestamp);
```

In L2StandERC20.sol

line 95

```solidity
modifier onlyBridge() {
require(msg.sender == l2Bridge);
_;
}
```

We recommend the project add error message to require statement.
Otherwise if the transactoin in the require without error message, 
it would be very difficult for both user and developer to find out why the transaction revert.

# Lack of two-step validator claim process.

### Line of code

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Governance.sol#L45

### Vulnerabilty detail and recommended fix

In the current implementation, the admin can just set a validator address to true or to false immediately.

```solidity
/// @notice Change validator status (active or not active)
/// @param _validator Validator address
/// @param _active Active flag
function setValidator(address _validator, bool _active) external onlyGovernor {
if (s.validators[_validator] != _active) {
s.validators[_validator] = _active;
emit ValidatorStatusUpdate(_validator, _active);
}
}
```

In the function Allowlist.sol and Governance.sol contract, two step transfer is implemented for admin role.
First, the admin set a pending admin, and the admin claim the admin role to prove that the new admin is intended and capable of managing the contract
as a admin.

However, the same pattern is missing when setting the validator.
Validator is really important because they are in charge of validatoring and committing the transaction state in zkSync system.
Letting the validator claim the validator proves the validator is capable to process the transaction and has the intention to do.