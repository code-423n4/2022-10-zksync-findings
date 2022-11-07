Unlocked pragma
Contracts should be deployed using the same compiler version/flags with which they have been tested. Locking the pragma (for e.g. by not using ^ in pragma solidity 0.5.10) ensures that contracts do not accidentally get deployed using an older compiler version with unfixed bugs.
https://swcregistry.io/docs/SWC-103
Ex:
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L3
==========================================================
