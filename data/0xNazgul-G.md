## [NAZ-G1] The Increment In For Loop Post Condition Can Be Made Unchecked
**Context**: [`Diamond.sol#L94`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L94), [`Diamond.sol#L132`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L132), [`Diamond.sol#L153`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L153), [`Diamond.sol#L173`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L173)

**Description**:
(This is only relevant if you are using the default solidity checked arithmetic). `i++` involves checked arithmetic, which is not required. This is because the value of `i` is always strictly less than length <= 2**256 - 1. Therefore, the theoretical maximum value of `i` to enter the for-loop body is `2**256 - 2`. This means that the `i++` in the for loop can never overflow. Regardless, the overflow checks are performed by the compiler.

Unfortunately, the Solidity optimizer is not smart enough to detect this and remove the checks. One can manually do this by:
```js
for (uint i = 0; i < length; ) {
    // do something that doesn't change the value of i
    unchecked {
        ++i;
    }
}
```

**Recommendation**: 
Consider doing the increment in the for loop post condition in an unchecked block.


## [NAZ-G2] Rearanging Setters To Save gas
**Context**: [`Governance.sol#L54`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L54), [`Governance.sol#L69`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L69), [`Governance.sol#L94`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L94), [`Governance.sol#L104`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L104)

**Description**:
Moving the event before setting the new value can save gas EX:
```js
contract UnOptimized {
    uint256 public value;

    event ValueUpdated(uint256 oldMinTarget, uint256 newMinTargetVotes);

    function updateValue(uint256 newValue) external {
        uint256 oldValue = value;
        value = newValue;

        emit ValueUpdated(oldValue, newValue);
    }
}


contract Optimized {
    uint256 public value;

    event ValueUpdated(uint256 oldValue, uint256 newValue);

    function updateValue(uint256 newValue) external {
        emit ValueUpdated(value, newValue);
        value = newValue;
    }
}
```

**Recommendation**: 
Consider Doing a similar code structure to save gas for setters.
