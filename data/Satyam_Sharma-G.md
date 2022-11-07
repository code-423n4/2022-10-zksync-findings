1.Functions marked as  payable  are 24 gas cheaper than their counterpart (in non-payable functions, Solidity adds an extra check to ensure msg.value is zero). When users can't mistakenly send ETH to a function (as an example, when there's an onlyOwner modifier or alike), it is safe to mark it as payable.

Mark payable can save gas:
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L57
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L140
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L113
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L89
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L65
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L22
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L35
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L46
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L78
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L91
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L15
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L45
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L54
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L69
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L84
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L94
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L104

2.Impact
using double require instead of operator && can save more gas

Proof of Concept
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L96

Tool : Remix