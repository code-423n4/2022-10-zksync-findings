## Table of Contents

- [L-01] _safemint() should be used rather than _mint() whereever possible
- [L-02] Lock floating pragma
- [N-01] Use a more recent version of solidity



### [L-01] _safemint() should be used rather than _mint() whereever possible

_mint()is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both OpenZeppelin and solmate have versions of this function.

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/bridge/L2StandardERC20.sol#L103
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/dev-contracts/TestnetERC20Token.sol#L19

### [L-02] Lock floating pragma

Non-library/interface files should use fixed compiler versions, not floating ones. 

For most non-library/interface contracts, eg
https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L103

### [N-01] Use a more recent version of solidity

Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings and get bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>. Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value. Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked (<str>,<str>