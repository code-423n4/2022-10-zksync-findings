**********L-01 Missing non-zero address checks in constructors**********

Almost all constructors in the code are missing non-zero value checks for their address arguments. Add such checks to all constructors.

************L-02 Use latest Solidity version************

Code primarily uses version `^0.8.0` so will work with solc 0.8.0. Always use latest Solidity version to get latest features and bugfixes.

********************L-03 Use concrete Solidity compiler version instead of a floating pragma********************

Code uses `^0.8.0` version. Using a floating pragma is discouraged and the best practice is considered to be to use a concrete Solidity version

**********NC-01 Code has open `TODO`s**

The code has open `TODO`s in multiple places. Fix all of them before deployment

**********************************************************************NC-02 Non-descriptive or missing error messages**********************************************************************

There are error messages in the code that are just `"l"` or `"t"` in the code. Also lots of require statements are just missing any error message. Add proper error messages in all require statements.

******NC-03 Code has unused private constants******

The constant *`VALUE_SIMULATOR_SYSTEM_CONTRACT_ADDRESS`* is defined in two places but is private and never read. Remove it.

************************************NC-04 Misleading NatSpec************************************

The doc in `LERC20Bridge::finalizeDeposit` says the following for the `_l1Token` param: `The address of the token that was locked on the L1. Always should be equal to zero (conventional value)` Second part is wrong (copy-pasted from the ETH bridge contract). Remove the `should be equal to zero` part

********************************************************************************************************************NC-5 NatSpec is missing doc for some function params********************************************************************************************************************

The NatSpec docs for `L2ETHBridge::withdraw` and `DiamondInit::initialize` and `DiamondCut::executeDiamondCutProposal` are missing docs for function parameters. Add the missing docs.

********************************************NC-6 Contract and filename are different********************************************

In `Mailbox.sol` we have a contract named `MailboxFacet` - use the same name in both places. ****************************************************************************************