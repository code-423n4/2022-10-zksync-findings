## Require Statements with Multiple Checks Need More Gas than Multiple Require Statements.

See link for a detailed discussion.
https://github.com/code-423n4/2022-01-xdefi-findings/issues/128

***

require(
    callersLength == _targets.length &&
        callersLength == _functionSigs.length &&
        callersLength == _enables.length,
    "yw"
); // The size of arrays should be equal
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L96-L101

require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol#L65