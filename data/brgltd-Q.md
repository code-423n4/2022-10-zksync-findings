# [01] Missing address zero checks on setter/constructor functions 

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L15

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#L27-L28

If a variable gets configured with address zero, failure to immediately reset the value can result in unexpected behavior for the project.

# [02] `initialize()` is susceptible to frontrun

For the L1 Eth bridge, the comment on [L47](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L47) indictates to call `initalize()` before a potential attacker (similar pattern in L1 ERC20 bridge). However, there's no access restriction on `initializa()` - anyone can call (and only once) after deployment.

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L56

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L71

## Recommeded Mitigation Steps

Conside adding access control on `initialize()`. If possible, add a check so that only the address that deploys the contract (and executes `constructor()`) can call `initialize()`.

# [03] Missing storage gap for upgradeable contracts

Consider adding a `__gap[]` storage variable to allow for new storage variables in later versions. [Example](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/Gap10000.sol).

See this [link](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) for a description of this storage variable issue. Adding the variable now protects against forgetting to add it in the future.

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L11

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L23

# [04] Values increased by rebase tokens will remain locked in `L1ERC20Bridge` if deposit fails

## Impact

The `balanceOf` rebase tokens (e.g. Aave aTokens) are increased over time. If such tokens are used in `deposit()` and the transaction fails on the L2 side, calls to `claimFailedDeposit()` won't account for the increased values. The value would only be meaningful if the deposit is large and the claim is made a reasonable time after the deposit.

## Proof of Concept

- User deposits a rebase ERC20 token, by calling `deposit()`
- The transaction fails on L2.
- User claims the failed deposit, by calling `claimFailedDeposit()`, the `amount` transfered back won't account for an increased `balanceOF`

## Recommendation

Take the increased `balanceOf` into account when computing the amount to be returned in `claimFailedDeposit()`, to support rebase tokens. 

Alternatively, if the gas costs and added code commplexity to add this functionality are considered unnecessary by the project, consider documenting that values increased by rebase tokens will be lost for ERC20 L1-L2 claims for failed deposits.

# [05] Lack of checks-effects-interactions

On `L1ERC20Bridge.initialize()` and `L1EthBridge.initialize()`, the state variable `l2Bridge` is updated after the `abi.encodeCall()` external call. Consider updating the state before external calls to follow the checks-effects-interactions pattern.

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L84

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L89

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L60

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L65

# [06] Avoid floating pragma where possible

Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version.

Note that pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or a package.

# [07] Outdated OpenZeppelin version

The project is using OpenZeppelin v4.6.0. Consider updating to the latest version: v4.7.3, to ensure the library contracts contain the latest security fixes.

# [08] Replace assert with require or custom error

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L16

As described on the solidity [documentation](https://docs.soliditylang.org/en/v0.8.15/control-structures.html#panic-via-assert-and-error-via-require). \"The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix.

Even if the code is expected to be unreacheable, consider using a require statement or a custom error instead of assert.

# [09] Avoid using the optimizer if possible, due to it's potential security bugs which can affect the contracts in scope

Optimizations are being actively developed and are not considered safe. Check the following [audit](https://github.com/trailofbits/publications/blob/master/reviews/SeaportProtocol.pdf) recommending agaist deployments with the optimizer enabled (item 3 in the audit document).

If possible, consider measuring and documenting the tradeoffs when enabling the optimizer.

# [10] Missing unit tests

Some external functions are missing unit tests (e.g. `DiamondCut.approveEmergencyDiamondCutAsSecurityCouncilMember()` and functions inside bridge contracts). Consider testing all code paths to improve the coverage.

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L105

# [11] Open TODOs

Todos should be resolved before deployment

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L94

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L127

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Config.sol#L28

# [12] Replace memory with calldata for read-only function arguments

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L167

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L260

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L214

# [13] Missing natspec/comments.

Consider adding natspec/comments on all functions to improve code documentation and readability.

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/ReentrancyGuard.sol#L43

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L116

# [14] Order of functions

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) recommends the following order for functions:

constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private

The instances bellow highlight a block with internal above public.

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L260-L261

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L282

# [15] Remove unused code

`Executor._calculateBlockHash()` is not used.

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L80-L86

# [16] Inconsistent use of named return variables

Some functions return named variables, others return explicit values.

Following function return explicit value

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L55

Following function return return a named variable

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L112

Consider adopting a consistent approach to return values throughout the codebase by removing all named return variables, explicitly declaring them as local variables, and adding the necessary return statements where appropriate. This would improve both the explicitness and readability of the code, and it may also help reduce regressions during future code refactors.

# [17] Public functions not called by the contract should be declared external

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L243

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L79
