1.
Title: Using multiple `require` instead `&&` can save gas

Proof of Concept:
[AllowList.sol#L96-L101](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L96-L101)

Recommended Mitigation Steps:
Change to:

```
        require(callersLength == _targets.length, "yw");
        require(callersLength == _functionSigs.length, "yw");
        require(callersLength == _enables.length, "yw");
```
________________________________________________________________________

2.
Title: Expression for `constant` values such as a call to `keccak256()`, should use `immutable` rather than `constant`

Proof of Concept:
[L2ContractHelper.sol#L24](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/L2ContractHelper.sol#L24)

Recommended Mitigation Steps:
Change from `constant` to `immutable`
reference: [here](https://github.com/ethereum/solidity/issues/9232)
________________________________________________________________________

3.
Title: Using `!=` in `require` statement is more gas efficient

Proof of Concept:
[L1ERC20Bridge.sol#L117](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L117)
[L1ERC20Bridge.sol#L210](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L210)

Recommended Mitigation Steps:
Change `> 0` to `!= 0`
________________________________________________________________________