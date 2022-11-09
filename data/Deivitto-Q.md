# QA
# Low

## Emitted amount can be bigger than expected
### Impact 
There are ERC20 tokens with transfer at fees. For checking if the transferred amount is the same as expected, code already compares balanceOf before and balanceOf after transfer. People can get confused in cases where real value doesn't match
### Github Permalinks
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L214-L216
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L254-L256
### Mitigation
Consider implementing same system as implemented:
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L141-L143



## Variable shadows another variable
###  Summary 
Name shadowing where two or more variables/functions share the same name could be confusing to developers and/or reviewers
###  Details 
Use of `facets` as input variable shadows `facets()`
###  Github Permalinks
- function  
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IGetters.sol#L66

- variable
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IGetters.sol#L70
###  Mitigation
Replace `facets` variable in the function parameter to `_facets`, `facets_` or a similar substitution


## Use of hardcoded amount of days
### Summary 
Formula uses a hardcoded value of `365` (days) which would be wrong applied in a leap year (`366` days)
### Github Permalinks

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Config.sol#L64
    defined(COMMIT_TIMESTAMP_NOT_OLDER) ? COMMIT_TIMESTAMP_NOT_OLDER : "365 days"

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Config.sol#L70
    defined(COMMIT_TIMESTAMP_APPROXIMATION_DELTA) ? COMMIT_TIMESTAMP_APPROXIMATION_DELTA : "365 days"

### Mitigation
Consider using an oracle for this
Consider using a method that change the value between `365` and `366` for the operations in leap years and regular years


## Missing checks for address(0x0) when assigning values to `address` state or `immutable` variables 
### Summary
Zero address should be checked for state variables, immutable variables. A zero address can lead into unexpected behaviors.
### Github Permalinks
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L57-L60
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L48-L51
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ERC20Bridge.sol#L36
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ERC20Bridge.sol#L41
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ETHBridge.sol#L31
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L79
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/DiamondInit.sol#L38-L39
### Mitigation
Check zero address before assigning or using it



## block.timestamp used as time proxy 
### Summary
Risk of using `block.timestamp` for time should be considered. 
### Details
`block.timestamp` is not an ideal proxy for time because of issues with synchronization, miner manipulation and changing block times. 

This kind of issue may affect the code allowing or reverting the code before the expected deadline, modifying the normal functioning or reverting sometimes.
### References
SWC ID: 116

### Github Permalinks 
- Dangerous comparisons with `block.timestamp`
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L52
        bool upgradeNoticePeriodPassed = block.timestamp >=


https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L45
        require(l2BlockTimestamp == _newBlock.timestamp);

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L50
            bool timestampNotTooSmall = block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= l2BlockTimestamp;

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L51
            bool timestampNotTooBig = l2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA;

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L52
            require(timestampNotTooSmall, "h"); // New block timestamp is too small

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L53
            require(timestampNotTooBig, "h1"); // New block timestamp is too big

- Time proxy storage
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L28
        s.diamondCutStorage.proposedDiamondCutTimestamp = block.timestamp;

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L85
        s.diamondCutStorage.lastDiamondFreezeTimestamp = block.timestamp;
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L75
                _newBlock.timestamp,

### Mitigation
- Consider the risk of using `block.timestamp` as time proxy and evaluate if block numbers can be used as an approximation for the application logic. Both have risks that need to be factored in. 
- Consider using an oracle for precision


## Front run initializer
### Summary
The initialize function that initializes important contract state can be called by anyone.
### Details
The attacker can initialize the contract before the legitimate deployer, hoping that the victim continues to use the same contract.

In the best case for the victim, they notice it and have to redeploy their contract costing gas.

### Github Permalinks
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/DiamondInit.sol#L25
    function initialize(

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L71
    function initialize(

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L56
    function initialize(bytes calldata _l2BridgeBytecode) external reentrancyGuardInitializer {

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L43
    function bridgeInitialize(address _l1Address, bytes memory _data) external initializer {


### Mitigation
Use the constructor to initialize non-proxied contracts.

For initializing proxy contracts deploy contracts using a factory contract that immediately calls initialize after deployment or make sure to call it immediately after deployment and verify the transaction succeeded.


## Return value not being checked 
### Details
Return values not being checked may lead into unexpected behaviors with functions. Not events/Error are being emitted if that fails, so functions would be called even of not being working as expect.

### Github Permalinks
- ignores return value by `zkSyncMailbox.requestL2Transaction`
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L73-L79
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L96-L102


### Mitigation
Check values and `revert`/`emit` events if needed


## `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccack256()`
### Summary
If you are dealing with more than one dynamic data type, `abi.encodePacked()` can lead to collisions when used with a hash function.

Use `abi.encode()` instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. `abi.encodePacked(0x123,0x456)` => `0x123456` => `abi.encodePacked(0x1,0x23456)`, but `abi.encode(0x123,0x456)` => `0x0...1230...456`). “Unless there is a compelling reason, `abi.encode` should be preferred”. If there is only one argument to `abi.encodePacked()` it can often be cast to `bytes()` or `bytes32()` instead.

### Details
abi.encodePacked will only use the only use the minimal required memory to encode the data. E.g. an address will only use 20 bytes and for dynamic arrays only the elements will be stored without length. For more info see the Solidity docs for packed mode.

For the input of the keccak method it is important that you can ensure that the resulting bytes of the encoding are unique. So if you always encode the same types and arrays always have the same length then there is no problem. But if you switch the parameters that you encode or encode multiple dynamic arrays you might have conflicts.

https://ethereum.stackexchange.com/questions/119583/when-to-use-abi-encode-abi-encodepacked-or-abi-encodewithsignature-in-solidity

### Github Permalinks
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L373
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L282-L291
### Mitigation
Change `abi.encodePacked` to `abi.encode` when data collision may happen


## Use of `asserts()`
### Impact

From solidity docs: Properly functioning code should never reach a failing assert statement; if this happens there is a bug in your contract which you should fix.
With assert the user pays the gas and with require it doesn't. The ETH network gas isn't cheap and users can see it as a scam.
You have reachable asserts in the following locations (which should be replaced by `require` / are mistakenly left from development phase):

### Details
The Solidity `assert()` function is meant to assert invariants. Properly functioning code should never reach a failing assert statement. A reachable assertion can mean one of two things:

A bug exists in the contract that allows it to enter an invalid state;
The assert statement is used incorrectly, e.g. to validate inputs.

### References
SWC ID: 110

### Github Permalinks
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L16
        assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);
### Recommended Mitigation Steps
Substitute `asserts` with `require`/`revert`.

# Informational
## Inconsistent version of english being used
Some functions use American English, whereas others use British English. A single project should use only one of the two

### Github Permalinks
Finalise & finalize
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ETHBridge.sol#L58
    /// NOTE: In order to get funds on L1, receiver should finalise deposit on L1 counterpart
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ETHBridge.sol#L35-L41
```

    /// @dev Finalize the deposit and mint ether to the deposited address
    /// @param _l1Sender The account address that initiate the deposit on L1
    /// @param _l2Receiver The account address that would receive minted ether
    /// @param _l1Token The address of the token that was locked on the L1. Always should be equal to zero (conventional value)
    /// @param _amount Total amount of ether deposited from L1
    function finalizeDeposit(
```


## Missing indexed event parameters
### Summary
Events without indexed event parameters make it harder and
inefficient for off-chain tools to analyze them.
### Details
Indexed parameters (“topics”) are searchable event parameters.
They are stored separately from unindexed event parameters in an efficient manner to allow for faster access. This is useful for efficient off-chain-analysis, but it is also more costly gas-wise.
 
### Github Permalinks

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L16
    event DiamondCut(FacetCut[] facetCuts, address initAddress, bytes initCalldata);

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L20
    event DiamondCutProposal(Diamond.FacetCut[] _facetCuts, address _initAddress);

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L24
    event DiamondCutProposalExecution(Diamond.DiamondCutData _diamondCut);

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L26
    event EmergencyFreeze();

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L28
    event Unfreeze(uint256 lastDiamondFreezeTimestamp);

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IExecutor.sol#L85
    event BlocksRevert(uint256 totalBlocksCommitted, uint256 totalBlocksVerified, uint256 totalBlocksExecuted);

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IGovernance.sol#L32
    event IsPorterAvailableStatusUpdate(bool isPorterAvailable);

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IGovernance.sol#L48
    event NewVerifierParams(VerifierParams oldVerifierParams, VerifierParams newVerifierParams);

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IMailbox.sol#L95
    event NewPriorityRequest(

### Mitigation
Consider which event parameters could be particularly useful to off-chain tools and should be indexed.

## Bad order of code
### Summary
Clearness of the code is important for the readability and maintainability.
As Solidity guidelines says about declaration order:
1.Type declarations
2.State variables
3.Events
4.Modifiers
5.Functions
Also, state variables order affects to gas in the same way as ordering structs for saving storage slots
### Github Permalink
- modifier should be declared before the constructor
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L37-L40

- modifier shouldn't be declared after functions
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/ReentrancyGuard.sol#L38-L86

- modifier shouldn't be declared after constructor and functions
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L95-L98

- struct between functions
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IGetters.sol#L1-L75

- events should be defined at first
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IGovernance.sol#L1-L49
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L1-L36
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IMailbox.sol#L1-L102
### Mitigation
Follow solidity style guidelines https://docs.soliditylang.org/en/v0.8.16/style-guide.html




## Missing Natspec 
### Summary 
Missing Natspec and regular comments affect readability and maintainability of a codebase. 
### Details 
Contracts has partial or full lack of comments
### Github Permalinks 
#### Natspec @param 
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Getters.sol#L73-L188
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L32-L81
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowListed.sol#L9-L18
#### Natspec @param and/or @return value
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L1-L389
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/L2ContractHelper.sol#L25-L43
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/ExternalDecoder.sol#L9-L17
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/interfaces/IL1Bridge.sol#L7-L14
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/interfaces/IL2EthInitializable.sol#L5-L7
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/interfaces/IL2StandardToken.sol#L1-L17
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L117-L290
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L1-L36
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IExecutor.sol#L62-L73
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IGetters.sol#L1-L75
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IGovernance.sol#L1-L49
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IMailbox.sol#L1-L102
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L1-L42
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/interfaces/IL2Bridge.sol#L1-L26
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/interfaces/IAllowList.sol#L27-L70
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/libraries/UncheckedMath.sol#L1-L17
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/libraries/UnsafeBytes.sol#L1-L45
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L213-L246
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L112-L122
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/L2ContractHelper.sol#L43-L87
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/interfaces/IL2Bridge.sol#L1-L40
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ETHBridge.sol#L57-L86
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ERC20Bridge.sol#L72-L80
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ERC20Bridge.sol#L103-L124
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L41-L130
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L136-L170
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L260-L287
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L116-L175
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L35-L96
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L122-L135
 ### mitigation
 - Add @param descriptors
 - Add @return descriptors
 - Add Natspec comments. 
 - Add inline comments. 
 - Add comments for what the contract does





## Unused code
### Summary
Code that is not used should be removed
### Github Permalinks
`ExecutorFacet._calculateBlockHash(IExecutor.StoredBlockInfo,IExecutor.CommitBlockInfo)` is never used and should be removed
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L80-L86

`L2ContractHelper.sendMessageToL1(bytes)` is never used and should be removed
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/L2ContractHelper.sol#L43-L45
### Mitigation
Remove the code that is not used.





## Commented out code
### Summary
There is some code that is commented out. It could point to items that are not done or need redesigning, be a mistake, or just be testing overhead.

### Github Permalinks
- code probably from solpp syntax commented
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L259
        // #if DUMMY_VERIFIER == false
- code commented for future situations
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L128-L129
        // uint256 baseCost = l2TransactionBaseCost(tx.gasprice, _ergsLimit, uint32(_calldata.length));
        // uint256 layer2Tip = msg.value - baseCost;

### Mitigation
Review and remove or resolve/document the commented out lines if needed.

## Different versions of pragma
### Summary
Some of the contracts include an unlocked pragma, e.g., pragma solidity ^0.8.0. 

Locking the pragma helps ensure that contracts are not accidentally deployed using an old compiler version with unfixed bugs.

### Github Permalinks
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/ExternalDecoder.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/L2ContractHelper.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ERC20Bridge.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ETHBridge.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/interfaces/IL1Bridge.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/interfaces/IL2Bridge.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/interfaces/IL2EthInitializable.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/interfaces/IL2StandardToken.sol

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/interfaces/IL1Bridge.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/interfaces/IL2Bridge.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowListed.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/L2ContractHelper.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/ReentrancyGuard.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/libraries/UncheckedMath.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/libraries/UnsafeBytes.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/interfaces/IAllowList.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/DiamondProxy.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/DiamondInit.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Config.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Storage.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Base.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Getters.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Governance.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Merkle.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/PriorityQueue.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IDiamondCut.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IExecutor.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IGetters.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IGovernance.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IMailbox.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IZkSync.sol

### Mitigation
Lock pragmas to a specific Solidity version. 
Consider converting ^0.8.0 into 0.8.17
Consider converting ^0.8 into 0.8.17



## Open TODOs   
### Summary
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment
### Details
The code includes a `TODO` that affects readability and focus on the readers/auditors of the contracts
### Github Permalinks
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Config.sol#L28
// TODO: change constant to the real root hash of empty Merkle tree (SMA-184)

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L94
        // TODO: estimate gas for L1 execute

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L127
        // TODO: Restore after stable priority op fee modeling. (SMA-1230)

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L169
                layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IExecutor.sol#L56
    /// TODO: The verifier integration is not finished yet, change the structure for compatibility later

    
### Mitigation
Remove already done `TODO`

## Missing error messages in require statements
### Summary
Require/revert statements should include error messages in order to help at monitoring the system.
### Github Permalinks
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ETHBridge.sol#L50
        require(_l1Token == CONVENTIONAL_ETH_ADDRESS);

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L96
        require(msg.sender == l2Bridge);

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L116
        if (availableGetters.ignoreName) revert();

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L122
        if (availableGetters.ignoreSymbol) revert();

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L128
        if (availableGetters.ignoreDecimals) revert();

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L43
        require(expectedNumberOfLayer1Txs == _newBlock.numberOfLayer1Txs);

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L45
        require(l2BlockTimestamp == _newBlock.timestamp);

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L297
        require(_recurisiveAggregationInput.length == 4);

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L145
        require(amount != 0);
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L221
        require(_message.length == 56);

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L224
        require(bytes4(functionSignature) == this.finalizeWithdrawal.selector);

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L238
        require(callSuccess);

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/DiamondProxy.sol#L47
                revert(ptr, size)

### Mitigation
Add error messages 

## Maximum line length exceeded
### Summary
Long lines should be wrapped to conform with Solidity Style guidelines. 
### Details 
Lines that exceed the 79 (or 99) character length suggested by the Solidity Style guidelines. Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length
### Github Permalinks 
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/DiamondProxy.sol#L12

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/DiamondProxy.sol#L35

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/DiamondInit.sol#L22

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/DiamondInit.sol#L24

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/DiamondInit.sol#L42

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Config.sol#L5

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Config.sol#L8

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Config.sol#L20

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Config.sol#L31

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Config.sol#L59

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Config.sol#L60

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Config.sol#L67

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Config.sol#L68

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Config.sol#L70

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Storage.sol#L9

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Storage.sol#L11

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Storage.sol#L12

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Storage.sol#L13

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Storage.sol#L14

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Storage.sol#L15

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Storage.sol#L16

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Storage.sol#L28

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Storage.sol#L29

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Storage.sol#L36

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Storage.sol#L37

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Storage.sol#L72

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Storage.sol#L80

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Storage.sol#L82

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Storage.sol#L84

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/Storage.sol#L94

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L15

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L21

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L22

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L23

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L25

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L26

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L27

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L43

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L46

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L55

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L84

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L103

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L104

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L106

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L108

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L109

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L111

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L112

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L113

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol#L114

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L23

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L28

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L50

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L51

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L56

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L80

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L103

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L111

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L122

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L136

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L152

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L159

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L164

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L176

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L177

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L189

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L190

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L192

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L198

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L199

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L208

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L212

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L216

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L237

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L260

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L264

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L296

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L337

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L354

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L362

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L373

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L376

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L381

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol#L385

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Getters.sol#L11

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Getters.sol#L51

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Getters.sol#L95

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Getters.sol#L100

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Getters.sol#L105

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Getters.sol#L122

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Getters.sol#L183

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Governance.sol#L12

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Governance.sol#L30

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Governance.sol#L63

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Governance.sol#L69

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Governance.sol#L78

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L20

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L22

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L23

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L59

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L100

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L103

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L105

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L112

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L113

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L128

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L169

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L174

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L178

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L179

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L184

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol#L215

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L9

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L11

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L14

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L20

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L37

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L38

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L41

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L62

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L71

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L72

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L79

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L87

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L88

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L135

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L156

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L176

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L234

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L263

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L276

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol#L281

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Merkle.sol#L12

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L5

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L8

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L38

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L71

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L8

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L22

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IExecutor.sol#L9

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IExecutor.sol#L29

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IExecutor.sol#L33

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IExecutor.sol#L38

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IExecutor.sol#L56

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IExecutor.sol#L62

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IExecutor.sol#L76

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IExecutor.sol#L79

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IExecutor.sol#L82

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IExecutor.sol#L85

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IGetters.sol#L52

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IGovernance.sol#L26

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IGovernance.sol#L29

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IGovernance.sol#L38

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IGovernance.sol#L39

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IMailbox.sol#L9

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IMailbox.sol#L10

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IMailbox.sol#L11

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IMailbox.sol#L12

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IMailbox.sol#L13

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IMailbox.sol#L14

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IMailbox.sol#L15

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IMailbox.sol#L16

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IMailbox.sol#L17

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IMailbox.sol#L18

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IMailbox.sol#L22

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IMailbox.sol#L23

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IMailbox.sol#L89

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IMailbox.sol#L92

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/interfaces/IMailbox.sol#L94

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L27

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L43

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L63

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L68

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L69

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L76

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L119

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L176

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L178

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L189

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L196

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L206

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L221

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L224

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L240

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L270

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol#L286

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L21

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L40

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L55

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L129

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L131

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L148

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L155

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L165

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L178

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L181

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L220

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol#L242

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L9

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L15

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L9

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L10

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L12

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L14

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L28

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L29

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L55

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L63

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L64

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L65

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L84

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L86

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L87

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L88

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L108

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L110

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L137

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol#L155

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowListed.sol#L13

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/L2ContractHelper.sol#L47

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/L2ContractHelper.sol#L55

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/L2ContractHelper.sol#L65

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/L2ContractHelper.sol#L72

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/ReentrancyGuard.sol#L27

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/ReentrancyGuard.sol#L29

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/ReentrancyGuard.sol#L54

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/ExternalDecoder.sol#L7

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ERC20Bridge.sol#L22

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ERC20Bridge.sol#L47

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ERC20Bridge.sol#L82

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ERC20Bridge.sol#L118

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ETHBridge.sol#L25

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ETHBridge.sol#L33

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ETHBridge.sol#L39

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ETHBridge.sol#L74

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L12

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L16

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L17

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L27

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L28

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L42

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L51

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L60

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L61

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L64

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L66

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L115

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L121

https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol#L127
### Mitigation
Reduce line length to less than 99 at least to improve maintainability and readability of the code 

## Require messages are not specific enough
### Impact
Require messages are not specific enough to understand what is going on and therefore, most of the times comments are included right after the require because otherwise it would be impossible to understand.
### Github permalinks
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/DiamondProxy.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Base.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/DiamondCut.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Executor.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Getters.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Governance.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/facets/Mailbox.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Diamond.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/Merkle.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/libraries/PriorityQueue.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1ERC20Bridge.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/bridge/L1EthBridge.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowList.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/AllowListed.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/L2ContractHelper.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/common/ReentrancyGuard.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ERC20Bridge.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2ETHBridge.sol
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/zksync/contracts/bridge/L2StandardERC20.sol

### Mitigation
Change the message to one that is easy to understand and remove comments in those places.


## Wrong comment
https://github.com/code-423n4/2022-10-zksync/blob/5a31c9db8ab32175dbd7264b05ce84931b6c0428/ethereum/contracts/zksync/DiamondProxy.sol#L7
/// @title Diamond Proxy Cotract (EIP-2535)//@audited should be Contract
### Mitigation
Fix the comment to Contract
