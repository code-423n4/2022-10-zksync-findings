## USE OF BLOCK.TIMESTAMP
Some contract code uses the block.timestamp as part of the calculations and time checks. Nevertheless, timestamps can be slightly altered by miners/validators to favor them in contracts that have logics that depend strongly on them.

Consider taking into account this issue and warning the users that such a scenario could happen. If the alteration of timestamps cannot affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future. Here are some instances found.

[Lines 50 - 51](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L50-L51)
[Line 28](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L28)
[Line 52](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L52)

## COMMENT AND CODE LINE LENGTH
Try limit the length of comments and/or code lines to 80 - 100 character long for readability sake. Here are some instances found.

[Line 104](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L104)
[Lines 14 - 17](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IMailbox.sol#L14-L17)
[Line 14](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L14)

## TIMELOCK FOR CRITICAL PARAMETER CHANGE
It is a good practice giving time to users to react and adjust to critical changes with a mandatory time window between the changes. The first step is simply broadcasting to users with a specific change that is coming whilst the second step commits that change after an appropriate period of waiting. This would allow time for users opposing to the change to withdraw within the set time frame. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of the owner making a malicious act). Specifically, privileged roles could use front running to make malicious changes just ahead of incoming transactions, or purely accidental negative effects could occur due to the unfortunate timing of changes. Here are the instances found.

[Lines 45 - 109](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L45-L109)

## COMMENTED CODE LINES
Codebase with lines of code commented out with `//` can lead to confusion and is detrimental to overall code readability. Consider removing commented out lines of code that are no longer needed. Here is an instance found.

[Lines 128 - 129](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L128-L129)

## TODO
Open TODO can point to an architecture or programming issue needing to be resolved. It is recommended resolving them before deploying.

Here are some of the instances found:

[Line 94](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L94)
[Line 127](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L127)
[Line 169](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L169)

## MISSING NATSPEC
As documented in the link below:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

It is recommended using a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more.

Here are some instances found.

[Line 80](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L80)
[Lines 372 - 382](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L372-L382)
[Lines 9 - 23](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IGovernance.sol#L9-L23)

## SANITY CHECKS
Zero address and zero value checks implemented at `initialize()` of `DiamondInit.sol` could avoid human errors leading to non-functional calls associated with the mistakes. This is especially so when the incidents entail `reentrancyGuardInitializer` preventing the function from getting re-initialized that could end up having had to redeploy the contract. 

## EVENT PARAMETERS SHOULD BE INDEXED
Up to three event parameters should be indexed. This will help filter off the logs in listening for specifically wanted data. Using `indexed` has the benefit of making the arguments log topics instead of data. 

Here are some instances found.

[Line 85](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L85)
[Line 20](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L20)
[Lines 95 - 101](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IMailbox.sol#L95-L101)

## USE OF DESCRIPTIVE VARIABLE NAMES AND MESSAGES
Non-descriptive local variables and require error messages could make code base difficult to read and navigate. Here are some of the instance found.

[Lines 29 - 30](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L29-L30)
[Line 22](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Base.sol#L22)
[Line 12](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Base.sol#L12)

## UNUSABLE EVENT
The following event is empty in the parameter field making it incapable of emitting anything. It is recommended removing this unusable line of code.

[Line 26](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L26)

## NOT USING OPENZEPPELIN CONTRACTS
OpenZeppelin maintains a library of standard, audited, community-reviewed, and battle-tested smart contracts. Instead of importing these contracts, they have been re-implemented. This increases the amount of code that the protocol team will have to maintain while missing all the improvements and bug fixes that the OpenZeppelin team is constantly implementing with the help of the community.

It is recommended importing the OpenZeppelin contracts instead of re-implementing or copying them. These contracts can be extended to add the extra functionalities if need be. Here is one of the instances found.

[`ReentrancyGuard.sol`](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/ReentrancyGuard.sol)

## L1BRIDGE COULD BE BRICKED
When unlocking funds for the valid withdraw request from L2, there is no added measures to detect any malicious attacks that could drain the contract balance if the attacker managed to dodge the verification system. Hackers could exploit via numerous attacking surfaces using low level proofs to hijack the tree system to receive token/ETH from L1 simply because there is a long enough delay to synchronize states on chain. An active contract balance check should be in place and pause `finalizeWithdrawal()` if a drastic drop in contract balance was detected.

It would be too late when `safeTransfer` or `_withdrawFunds()` reverted considering all funds would have been drained. This is a prevailing issue in cross-chain non-atomic transfer.

## DOS ON UNBOUNDED LOOP
Unbounded loop could lead to OOG (Out of Gas) denying the users' of needed services. Here are some instances found.

[Line 94](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L94)
[Line 173](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L173)

## TYPO ERRORS
[Line 36](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#L36)

```
@ addreses should be corrected to addresses
/// @dev The sender is an `address` type, although we are using `uint256` for addreses in `L2CanonicalTransaction`.
```
[Line 63](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L63)

```
@ dellegate should be corrected to delegate     
/// @param initAddress The address that's dellegate called after setting up new facet changes
```
[Line 7](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L7)

```
@ Cotract should be corrected to Contract
/// @title Diamond Proxy Cotract (EIP-2535)
```
## STORAGE GAP FOR UPGRADEABLE CONTRACTS
Consider adding a storage gap at the end of each upgradeable contract (Openzeppelin associated). In the event some contracts needed to inherit from them, there would not be an issue shifting down of storage in the inheritance chain. Generally, storage gaps are a novel way of reserving storage slots in a base contract, allowing future versions of that contract to use up those slots without affecting the storage layout of child contracts. If not, the variable in the child contract might be overridden whenever new variables are added to it. This could have unintended and vulnerable consequences to the child contracts.