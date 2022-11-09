# [L] There is no check if the input parameter same as current state

Inside `Governance.sol` contract, most function check if the input is not equal to the current state var, but this `setVerifierParams()` function is missing the check. So if being called with the same value as the current state, it will success, even though there is no change. Consider to check if the input is not equal to current state.

```solidity
File: Governance.sol
104:     function setVerifierParams(VerifierParams calldata _newVerifierParams) external onlyGovernor {
105:         VerifierParams memory oldVerifierParams = s.verifierParams;
106: 
107:         s.verifierParams = _newVerifierParams;
108:         emit NewVerifierParams(oldVerifierParams, _newVerifierParams);
109:     }
```

# [NC] Revert if no changes to state

Most of `Governance.sol` function contains `if` statement to check whether the provided input for a variable is not the same as the current state variable. If the input is same or equal, then the function did not revert or abort, it's better to `revert` or `require` so there will be no transaction happen if the input is same or equal to current state variable.

for example:
```solidity
File: Governance.sol
094:     function setVerifier(Verifier _newVerifier) external onlyGovernor {
095:         Verifier oldVerifier = s.verifier;
096:         if (oldVerifier != _newVerifier) {
097:             s.verifier = _newVerifier;
098:             emit NewVerifier(address(oldVerifier), address(_newVerifier));
099:         }
100:     }
```

can be rewrite to 
```
    function setVerifier(Verifier _newVerifier) external onlyGovernor {
        Verifier oldVerifier = s.verifier;
        require (oldVerifier != _newVerifier, "Same as current state"); 

        s.verifier = _newVerifier;
        emit NewVerifier(address(oldVerifier), address(_newVerifier));
    }
```

the `setPendingGovernor()`, `acceptGovernor()`, `setValidator()`, `setL2BootloaderBytecodeHash()`, `setL2DefaultAccountBytecodeHash()` , `setPorterAvailability()` and `setVerifierParams()` also related to this.


# [NC] Replace assert with require

```solidity
File: DiamondCut.sol
13:     constructor() {
14:         // Caution check for config value.
15:         // Should be greater than 0, otherwise zero approvals will be enough to make an instant upgrade!
16:         assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);
17:     }
```

# [NC] Commented code

There is still code commented, if the code is not being used anymore just remove it. Keeping commented code will give an impression of cluttered design and unfinished works.

```solidity
File: Mailbox.sol
128:         // uint256 baseCost = l2TransactionBaseCost(tx.gasprice, _ergsLimit, uint32(_calldata.length));
129:         // uint256 layer2Tip = msg.value - baseCost;
```

# [NC] Open TODO

```solidity
File: Mailbox.sol
89:     function l2TransactionBaseCost(
90:         uint256, // _gasPrice
91:         uint256, // _ergsLimit
92:         uint32 // _calldataLength
93:     ) public pure returns (uint256) {
94:         // TODO: estimate gas for L1 execute
95:         return 0;
96:     }
...
127:         // TODO: Restore after stable priority op fee modeling. (SMA-1230)
...
165:         s.priorityQueue.pushBack(
166:             PriorityOperation({
167:                 canonicalTxHash: canonicalTxHash,
168:                 expirationBlock: _expirationBlock,
169:                 layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)
170:             })
171:         );
```