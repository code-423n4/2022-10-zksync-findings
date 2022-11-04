## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | Missing Checks for Address(0x0)  | 5 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Missing Contract-existence Checks Before Low-level Calls | 1 |
| [LOW&#x2011;3](#LOW&#x2011;3) | Contracts are not using their OZ Upgradeable counterparts | 8 |
| [LOW&#x2011;4](#LOW&#x2011;4) | Missing parameter validation in `constructor` | 3 |

Total: 17 contexts over 4 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Critical Changes Should Use Two-step Procedure | 10 |
| [NC&#x2011;2](#NC&#x2011;2) | Use a more recent version of Solidity | All in-scope contracts |
| [NC&#x2011;3](#NC&#x2011;3) | Redundant Cast | 1 |
| [NC&#x2011;4](#NC&#x2011;4) | Event Is Missing Indexed Fields | 5 |
| [NC&#x2011;5](#NC&#x2011;5) | `require()` / `revert()` Statements Should Have Descriptive Reason Strings | 12 |
| [NC&#x2011;6](#NC&#x2011;6) | Implementation contract may not be initialized | 8 |
| [NC&#x2011;7](#NC&#x2011;7) | Avoid Floating Pragmas: The Version Should Be Locked | All in-scope contracts |
| [NC&#x2011;8](#NC&#x2011;8) | Non-usage of specific imports | 77 |
| [NC&#x2011;9](#NC&#x2011;9) | Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant` | 2 |
| [NC&#x2011;10](#NC&#x2011;10) | Lines are too long | 8 |
| [NC&#x2011;11](#NC&#x2011;11) | Open TODOs | 5 |

Total: 206 contexts over 11 issues

## Low Risk Issues

### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Missing Checks for Address(0x0) 

Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

#### <ins>Proof Of Concept</ins>


```
73: function initialize: address _l2TokenFactory
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L73

```
113: function deposit: address _l1Token
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#LL113

```
180: function claimFailedDeposit: address _depositSender
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L180

```
133: function claimFailedDeposit: address _depositSender
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L133

```
102: function bridgeMint: address _to
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L102



#### <ins>Recommended Mitigation Steps</ins>

Consider adding explicit zero-address validation on input parameters of address type.



### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Missing Contract-existence Checks Before Low-level Calls

Low-level calls return success if there is no code present at the specified address. 

#### <ins>Proof Of Concept</ins>


```
282: (bool success, bytes memory data) = _init.delegatecall(_calldata);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L282




#### <ins>Recommended Mitigation Steps</ins>

Add a check to verify that `_init.code.length > 0`



### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> Contracts are not using their OZ Upgradeable counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

#### <ins>Proof of Concept</ins>

```
5: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
6: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L5-L6

```
5: import "@openzeppelin/contracts/proxy/beacon/BeaconProxy.sol";
6: import "@openzeppelin/contracts/proxy/beacon/UpgradeableBeacon.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L5-L6



#### <ins>Recommended Mitigation Steps</ins>

Where applicable, use the contracts from `@openzeppelin/contracts-upgradeable` instead of `@openzeppelin/contracts`.



### <a href="#Summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Missing parameter validation in `constructor`

Some parameters of constructors are not checked for invalid values.

#### <ins>Proof Of Concept</ins>

```
32: address _l1Bridge
34: address _governor
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L32-L34

```
30: address _l1Bridge
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol#L30



#### <ins>Recommended Mitigation Steps</ins>

Validate the parameters.


## Non Critical Issues

### <a href="#Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Critical Changes Should Use Two-step Procedure

The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure

#### <ins>Proof Of Concept</ins>

```
57: function setPublicAccess(address _target, bool _enable) external onlyOwner {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L57

```
65: function setBatchPublicAccess(address[] calldata _targets, bool[] calldata _enables) external onlyOwner {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L65

```
89: function setBatchPermissionToCall(
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L89

```
113: function setPermissionToCall(
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L113

```
45: function setValidator(address _validator, bool _active) external onlyGovernor {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L45

```
54: function setL2BootloaderBytecodeHash(bytes32 _l2BootloaderBytecodeHash) external onlyGovernor {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L54

```
69: function setL2DefaultAccountBytecodeHash(bytes32 _l2DefaultAccountBytecodeHash) external onlyGovernor {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L69

```
84: function setPorterAvailability(bool _zkPorterIsAvailable) external onlyGovernor {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L84

```
94: function setVerifier(Verifier _newVerifier) external onlyGovernor {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L94

```
104: function setVerifierParams(VerifierParams calldata _newVerifierParams) external onlyGovernor {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L104



#### <ins>Recommended Mitigation Steps</ins>

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.



### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Use a more recent version of Solidity

Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

#### <ins>Proof Of Concept</ins>

Relevant to all in-scope contracts.



#### <ins>Recommended Mitigation Steps</ins>

Consider updating to a more recent solidity version.



### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Redundant Cast

The type of the variable is the same as the type to which the variable is being cast

#### <ins>Proof Of Concept</ins>


```
206: uint256(_txId)
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol#L206






### <a href="#Summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Event Is Missing Indexed Fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). 

Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

#### <ins>Proof Of Concept</ins>


```
event DiamondCutProposal(Diamond.FacetCut[] _facetCuts, address _initAddress);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L20

```
event BlocksRevert(uint256 totalBlocksCommitted, uint256 totalBlocksVerified, uint256 totalBlocksExecuted);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L85

```
event NewVerifierParams(VerifierParams oldVerifierParams, VerifierParams newVerifierParams);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IGovernance.sol#L48

```
event NewPriorityRequest(
        uint256 txId,
        bytes32 txHash,
        uint64 expirationBlock,
        L2CanonicalTransaction transaction,
        bytes[] factoryDeps
    );
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IMailbox.sol#L95

```
event DiamondCut(FacetCut[] facetCuts, address initAddress, bytes initCalldata);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L16






### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> `require()` / `revert()` Statements Should Have Descriptive Reason Strings

#### <ins>Proof Of Concept</ins>


```
145: require(amount != 0);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L145

```
221: require(_message.length == 56);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L221

```
224: require(bytes4(functionSignature) == this.finalizeWithdrawal.selector);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L224

```
238: require(callSuccess);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L238

```
43: require(expectedNumberOfLayer1Txs == _newBlock.numberOfLayer1Txs);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L43

```
45: require(l2BlockTimestamp == _newBlock.timestamp);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L45

```
297: require(_recurisiveAggregationInput.length == 4);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L297

```
50: require(_l1Token == CONVENTIONAL_ETH_ADDRESS);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol#L50

```
96: require(msg.sender == l2Bridge);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L96

```
116: revert();
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L116

```
122: revert();
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L122

```
128: revert();
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L128






### <a href="#Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

#### <ins>Proof Of Concept</ins>


```
57: constructor(IMailbox _mailbox, IAllowList _allowList) reentrancyGuardInitializer {
58:     zkSyncMailbox = _mailbox;
59:     allowList = _allowList;
60: }
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L57-L60

```
48: constructor(IMailbox _mailbox, IAllowList _allowList) reentrancyGuardInitializer {
49:     zkSyncMailbox = _mailbox;
50:     allowList = _allowList;
51: }
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L48-L51

```
32: constructor(address _owner) {
33:     require(_owner != address(0), "kq");
34:     owner = _owner;
35: }
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L32-L35

```
10: constructor(uint256 _chainId, Diamond.DiamondCutData memory _diamondCut) {
13:     require(_chainId == block.chainid, "pr");
14:     Diamond.diamondCut(_diamondCut);
15: }
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondProxy.sol#L10-L15

```
13: constructor() {
16:     assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);
17: }
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L13-L17

```
    constructor(
        address _l1Bridge,
        bytes32 _l2TokenProxyBytecodeHash,
        address _governor
    ) {
        l1Bridge = _l1Bridge;

        l2TokenProxyBytecodeHash = _l2TokenProxyBytecodeHash;
        address l2StandardToken = address(new L2StandardERC20{salt: bytes32(0)}());
        l2TokenFactory = new UpgradeableBeacon{salt: bytes32(0)}(l2StandardToken);
        l2TokenFactory.transferOwnership(_governor);
    }
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L31-L42

```
    constructor(address _l1Bridge) {
        l1Bridge = _l1Bridge;

        IL2EthInitializable(address(ETH_TOKEN_SYSTEM_CONTRACT_ADDRESS)).initialization(address(this));
    }
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol#L30-L34





### <a href="#Summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Avoid Floating Pragmas: The Version Should Be Locked

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### <ins>Proof Of Concept</ins>

Found usage of floating pragmas ^0.8.0 of Solidity in all in-scope contracts.


### <a href="#Summary">[NC&#x2011;8]</a><a name="NC&#x2011;8"> Non-usage of specific imports

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

#### <ins>Proof Of Concept</ins>


```
8: import "./interfaces/IL1Bridge.sol";
9: import "./interfaces/IL2Bridge.sol";
11: import "../common/interfaces/IAllowList.sol";
12: import "../common/AllowListed.sol";
13: import "../common/libraries/UnsafeBytes.sol";
14: import "../common/ReentrancyGuard.sol";
15: import "../common/L2ContractHelper.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L8-L15

```
5: import "./interfaces/IL1Bridge.sol";
6: import "./interfaces/IL2Bridge.sol";
8: import "../common/interfaces/IAllowList.sol";
9: import "../common/AllowListed.sol";
10: import "../common/libraries/UnsafeBytes.sol";
11: import "../common/L2ContractHelper.sol";
12: import "../common/ReentrancyGuard.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L5-L12

```
5: import "./interfaces/IAllowList.sol";
6: import "./libraries/UncheckedMath.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L5-L6

```
5: import "./interfaces/IAllowList.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowListed.sol#L5

```
5: import "../common/interfaces/IAllowList.sol";
6: import "./interfaces/IExecutor.sol";
7: import "./libraries/Diamond.sol";
8: import "./facets/Base.sol";
9: import "./Config.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondInit.sol#L5-L9

```
5: import "./libraries/Diamond.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondProxy.sol#L5

```
5: import "./Verifier.sol";
6: import "../common/interfaces/IAllowList.sol";
7: import "./libraries/PriorityQueue.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Storage.sol#L5-L7

```
5: import "../Storage.sol";
6: import "../../common/ReentrancyGuard.sol";
7: import "../../common/AllowListed.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Base.sol#L5-L7

```
5: import "../interfaces/IDiamondCut.sol";
6: import "../libraries/Diamond.sol";
7: import "../Config.sol";
8: import "./Base.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L5-L8

```
5: import "./Base.sol";
6: import "../Config.sol";
7: import "../interfaces/IExecutor.sol";
8: import "../libraries/PairingsBn254.sol";
9: import "../libraries/PriorityQueue.sol";
10: import "../../common/libraries/UncheckedMath.sol";
11: import "../../common/libraries/UnsafeBytes.sol";
12: import "../../common/L2ContractHelper.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L5-L12

```
5: import "./Base.sol";
6: import "../libraries/Diamond.sol";
7: import "../libraries/PriorityQueue.sol";
8: import "../../common/libraries/UncheckedMath.sol";
9: import "../interfaces/IGetters.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Getters.sol#L5-L9

```
5: import "../interfaces/IGovernance.sol";
6: import "../../common/L2ContractHelper.sol";
7: import "./Base.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L5-L7

```
5: import "../interfaces/IMailbox.sol";
6: import "../libraries/Merkle.sol";
7: import "../libraries/PriorityQueue.sol";
8: import "../Storage.sol";
9: import "../Config.sol";
10: import "../../common/libraries/UncheckedMath.sol";
11: import "../../common/L2ContractHelper.sol";
12: import "./Base.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol#L5-L12

```
5: import "../libraries/Diamond.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol#L5

```
5: import "../libraries/PriorityQueue.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IGetters.sol#L5

```
5: import "../Verifier.sol";
6: import "../Storage.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IGovernance.sol#L5-L6

```
5: import "./IMailbox.sol";
6: import "./IGovernance.sol";
7: import "./IExecutor.sol";
8: import "./IDiamondCut.sol";
9: import "./IGetters.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IZkSync.sol#L5-L9

```
5: import "../../common/libraries/UncheckedMath.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Merkle.sol#L5

```
8: import "./interfaces/IL1Bridge.sol";
9: import "./interfaces/IL2Bridge.sol";
10: import "./interfaces/IL2StandardToken.sol";
12: import "./L2StandardERC20.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L8-L12

```
7: import "./interfaces/IL1Bridge.sol";
8: import "./interfaces/IL2Bridge.sol";
9: import "./interfaces/IL2EthInitializable.sol";
10: import "./interfaces/IL2StandardToken.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol#L7-L10

```
6: import "./interfaces/IL2StandardToken.sol";
7: import "../ExternalDecoder.sol";
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L6-L7




#### <ins>Recommended Mitigation Steps</ins>

Use specific imports syntax per solidity docs recommendation.



### <a href="#Summary">[NC&#x2011;9]</a><a name="NC&#x2011;9"> Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the constructor.

#### <ins>Proof Of Concept</ins>

```
41: bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol#L41


```
24: bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/L2ContractHelper.sol#L24



### <a href="#Summary">[NC&#x2011;10]</a><a name="NC&#x2011;10"> Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length
Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length

#### <ins>Proof Of Concept</ins>

```
59: /// @dev NOTE: we must reserve for users enough time to send full exit operation, wait maximum time for processing this operation and withdraw funds from it.
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Config.sol#L59

```
104: /// @param _diamondCutHash The hash of the diamond cut that security council members want to approve. Needed to prevent unintentional approvals, including reorg attacks
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L104


```
9: /// @param indexRepeatedStorageChanges The serial number of the shortcut index that's used as a unique identifier for storage keys that were used twice or more
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L9

```
29: /// @param indexRepeatedStorageChanges The serial number of the shortcut index that's used as a unique identifier for storage keys that were used twice or more
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L29

```
14: /// @param ergsPerPubdataByteLimit Maximum number of ergs that will cost one byte of pubdata (every piece of data that will be stored on L1 as calldata)
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IMailbox.sol#L14

```
15: /// @param maxFeePerErg The absolute maximum sender willing to pay per unit of ergs to get the transaction included in a block. Analog to the EIP-1559 `maxFeePerGas` on an L1 transactions
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IMailbox.sol#L15

```
16: /// @param maxPriorityFeePerErg The additional fee that is paid directly to the validator to incentivize them to include the transaction in a block. Analog to the EIP-1559 `maxPriorityFeePerGas` on an L1 transactions
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IMailbox.sol#L16

```
17: /// @param paymaster The address of the EIP-4337 paymaster, that will pay fees for the transaction. `uint256` type for possible address format changes and maintaining backward compatibility
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IMailbox.sol#L17



### <a href="#Summary">[NC&#x2011;11]</a><a name="NC&#x2011;11"> Open TODOs

An open TODO is present. It is recommended to avoid open TODOs as they may indicate programming errors that still need to be fixed.

#### <ins>Proof Of Concept</ins>


```
28: // TODO: change constant to the real root hash of empty Merkle tree (SMA-184)
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Config.sol#L28

```
94: // TODO: estimate gas for L1 execute
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol#L94

```
127: // TODO: Restore after stable priority op fee modeling. (SMA-1230)
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol#L127

```
169: layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol#L169

```
56: /// TODO: The verifier integration is not finished yet, change the structure for compatibility later
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IExecutor.sol#L56
