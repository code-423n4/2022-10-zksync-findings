## `block.timestamp` is unreliable

Using block.timestamp as part of the time checks could be slightly modified by miners/validators to favor them in contracts that contain logic heavily dependent on them.

Consider this problem and warn users that a scenario like this could occur. If the change of timestamps will not affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future.

Here are some of the instances:
[Line 28](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L28), [Line 52](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L52), [Line 85](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L85), [Line 50-51](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L50-L51)

## Unspecific Compiler Version Pragma
Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

For example:
🤦 Bad:
```
pragma solidity ^0.8.0;
```
🚀 Good:
```
pragma solidity 0.8.4;
```

for more info:
[Consensys Audit of 1inch](https://consensys.net/diligence/audits/2020/12/1inch-liquidity-protocol/#unspecific-compiler-version-pragma)

[Solidity docs](https://docs.soliditylang.org/en/latest/layout-of-source-files.html#version-pragma)
