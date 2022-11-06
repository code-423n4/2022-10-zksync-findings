* Error message is not delivered and thrown
  - [`Diamond.sol - _initializeDiamondCut`](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L283): the error returned by delegatecall may be diverse.

* Lack of validity checking when setting important storage states
  - [DiamondInit.sol - initialize](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/DiamondInit.sol#L25-L62): require `_governor, _validator, _genesisBlockHash` to be none-zero, and `_verifier, _allowList` to be existing contracts.
  - [Governance.sol - setVerifier](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Governance.sol#L94-L100): require `_newVerifier` to be an existing contract.
  - [L1ERC20Bridge.sol - constructor](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1ERC20Bridge.sol#L57-L60): require `_mailbox, _allowList` to be existing contracts.
  - [L1EthBridge.sol - constructor](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1EthBridge.sol#L48-L51): require `_mailbox, _allowList` to be existing contracts.

* The comment does not match the function
  - Miss parameters in comments for [DiamondInit.sol - initialize](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/DiamondInit.sol#L17-L37)
