* Use shift operation instead of `x / 2`, because shift cost less gas and can avoid unnecessary overflow checks.
  - [Merkle.sol#L34)](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/libraries/Merkle.sol#L34)
    ```
    _index /= 2;
    ```

* Check `require`s ealier if possible to make the failed transaction ends early and save gas.
  - Move [DiamondProxy.sol#L24](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/DiamondProxy.sol#L24) to top of the function.
    ```
    require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
    ```
  - Move [DiamondCut.sol#L40](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/DiamondCut.sol#L40) to top of the function.
    ```
    require(_resetProposal(), "g1"); // failed cancel diamond cut
    ```
  - Move [Executor.sol#L216](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L216) to top of the function.
    ```
    require(s.totalBlocksExecuted <= s.totalBlocksVerified, "n"); // Can't execute blocks more then committed and proven currently.
    ```
  - Move [Executor.sol#L268](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L268) to top of the function.
    ```
    require(currentTotalBlocksVerified <= s.totalBlocksCommitted, "q");
    ```
* Splitting `require()` statements that use `&&` saves gas
  - [AllowList.sol#L96-L101](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/AllowList.sol#L96-L101)
    ```
    require(
      callersLength == _targets.length &&
          callersLength == _functionSigs.length &&
          callersLength == _enables.length,
      "yw"
    ); // The size of arrays should be equal
    ```
  - [L2ContractHelper.sol#L65](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/L2ContractHelper.sol#L65)
    ```
    require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
    ```


