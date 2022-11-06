## TABLE OF CONTENTS

- [G-01] Use a more recent version of Solidity 
- [G-02] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

## [G-01] Use a more recent version of Solidity 

Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining. 
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads. 
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings and get bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>. 
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value. 
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked (<str>,<str>)

For example: 
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/dev-contracts/TestnetERC20Token.sol#L3

## [G-02] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed.

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/dev-contracts/TestnetERC20Token.sol#L8

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2StandardERC20.sol#L29