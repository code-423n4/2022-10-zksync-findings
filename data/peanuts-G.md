## Table of Contents

- [G-01] Use a more recent version of solidity
- [G-02] Internal functions only called once can be inlined to save gas
- [G-03] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead


### [G-01] Use a more recent version of solidity

Use a solidity version of at least 0.8.2 to get simple compiler automatic in-lining. Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads. Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings.

All contracts

### [G-02] Internal functions only called once can be inlined to save gas

Not in-lining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

[l1ERC20Bridge.sol](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L136-L140)

### [G-03] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed. https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html

[L2StandardERC20.sol](https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2StandardERC20.sol#L29)