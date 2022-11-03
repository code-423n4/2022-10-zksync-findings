# L-01 Use the latest version of OpenZeppelin

To prevent any issues in the future (e.g. using solely hardhat to compile and deploy the contracts), upgrade the used OZ packages within the package.json to the latest versions.

- https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/package.json#L8

### Recommended Mitigation Steps:

Consider using the latest OZ packages (v4.7.3) within package.json.

==============================

# N-01 MISSING ZERO ADDRESS CHECK FOR `_l2Receiver`

_l2Receiver is the account address that would receive minted ether

- https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ERC20Bridge.sol#L52

### Recommended Mitigation Steps:

Consider adding a zero address check for `_l2Receiver`

=============================

# N-02 Typos

There are a few typos in the contract's comments:

1.Stores the L1 address of the bridge and set `...`/`...`/`decimls` - `decimals` *
- https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2StandardERC20.sol#L42

2.It is standard implementation of ERC20 Bridge that can be used as a `refference` - `reference` *
- https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1ERC20Bridge.sol#L19

3.We are expecting to see the `exect` two bytecodes  - `exact` *
- https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1ERC20Bridge.sol#L76

4.@title Diamond Proxy `Cotract` (EIP-2535) - `Contract` *
- https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/DiamondProxy.sol#L7

