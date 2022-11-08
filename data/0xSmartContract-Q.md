## Summary

### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]| Apply two-step verification if `_l2Receiver` is not msg.sender |  |
|[L-02]|Very critical `governor` privileges can cause complete destruction of the project in a possible privateKey exploit| 12 |
|[L-03]|Incorrect NatSpec in `deposit` function for `_amount`|  |
|[L-04]|Require messages are too short and unclear| 78 |
|[L-05]|If the `_amount` sent to L2 is lower than the minimum tx value, the user cannot use the fund in L2|  |

Total 5 issues

### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [NC-01 ]| `_withdrawFunds` function create dirty bits | |
| [NC-02] |Insufficient coverage ||
| [NC-03] |Using Vulnerable Version of Openzeppelin| 2 |
| [NC-04] |`0 address` check| 2 |
| [NC-05] | `Function writing` that does not comply with the `Solidity Style Guide` | All contracts |
| [NC-06] | Omissions in Events | 1 |
| [NC-07] |Add parameter to Event-Emit | 2 |
| [NC-08] | Use a more recent version of Solidity | All contracts |
| [NC-09] | Solidity compiler optimizations can be problematic|  |
| [NC-10] | NatSpec is missing  | 1 |
| [NC-11] | Require revert cause should be known | 7 |
| [NC-12] | Open TODOs | 5 |
| [NC-13] | For modern and more readable code; update import usages | All contracts |
| [NC-14] | Use `require` instead of `assert` | 1 |

Total 14 issues

### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |Generate perfect code headers every time |
| [S-02] |Add NatSpec comments to the variables defined in Storage|

Total 2 suggestions

### [L-01] Apply two-step verification if `_l2Receiver` is not msg.sender

`_l2Receiver` in the deposit function is an important parameter to bridge Ether, this parameter is expected to be equal to msg.sender in most cases, but if another address is incorrectly typed (could be copy-paste errors), all funds will be lost, so you can use msg.sender for this argument. In case .sender is not equal, add an additional check, this will increase security
The deposit function is the most important function of a bridge.

Another L2 Project is the subject of Winturmute hack in Optimism; It was a token sent to the wrong address;

https://www.coinspeaker.com/optimism-20m-op-tokens-hackers-winterute/

```solidity
    /// @param _amount The total amount of ether to be bridged
    /// @return txHash The L2 transaction hash of deposit finalization    
function deposit(
        address _l2Receiver,
        address _l1Token,
        uint256 _amount
    ) external payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 txHash) {
        require(_l1Token == CONVENTIONAL_ETH_ADDRESS, "bx");

        // Will revert if msg.value is less than the amount of the deposit
        uint256 zkSyncFee = msg.value - _amount;
        bytes memory l2TxCalldata = _getDepositL2Calldata(msg.sender, _l2Receiver, _amount);
        txHash = zkSyncMailbox.requestL2Transaction{value: zkSyncFee}(
            l2Bridge,
            0, // L2 msg.value
            l2TxCalldata,
            DEPOSIT_ERGS_LIMIT,
            new bytes[](0)
        );

        // Save the deposit amount, to claim funds back if the L2 transaction will failed
        depositAmount[msg.sender][txHash] = _amount;

        emit DepositInitiated(msg.sender, _l2Receiver, _l1Token, _amount);
    }
```

### [L-02]  Very critical `governor` privileges can cause complete destruction of the project in a possible privateKey exploit


### Vulnerability details
The contract’s `governor` is most important power role in this project. the governor is able to perform certain privileged activities.


The `governor` is assumed to be an EOA, since the documents do not provide information on whether the `governor`  will be a multisign structure.


Similar vulnerability;
Private keys stolen:

Hackers have stolen cryptocurrency worth around €552 million from a blockchain project linked to the popular online game Axie Infinity, in one of the largest cryptocurrency heists on record. Security issue : PrivateKey of the project officer was stolen:
https://www.euronews.com/next/2022/03/30/blockchain-network-ronin-hit-by-552-million-crypto-heist

This vulnerability can also be considered as a Rugpull risk.

### Proof of Concept

`governor` powers;
```solidity

ethereum/contracts/zksync/facets/DiamondCut.sol:
  21      /// @param _initAddress Address of the fallback contract that will be called after the upgrade execution
  22:     function proposeDiamondCut(Diamond.FacetCut[] calldata _facetCuts, address _initAddress) external onlyGovernor {
  23          require(s.diamondCutStorage.proposedDiamondCutTimestamp == 0, "a3"); // proposal already exists

  34      /// @notice Removes the upgrade proposal. Only current governor can remove proposal.
  35:     function cancelDiamondCutProposal() external onlyGovernor {
  36          emit DiamondCutProposalCancelation(

  45      /// `initCalldata` can be arbitrarily.
  46:     function executeDiamondCutProposal(Diamond.DiamondCutData calldata _diamondCut) external onlyGovernor {
  47          Diamond.DiamondStorage storage diamondStorage = Diamond.getDiamondStorage();

  77      /// @notice Instantly pause the functionality of all freezable facets & their selectors
  78:     function emergencyFreezeDiamond() external onlyGovernor {
  79          Diamond.DiamondStorage storage diamondStorage = Diamond.getDiamondStorage();

  90      /// @notice Unpause the functionality of all freezable facets & their selectors
  91:     function unfreezeDiamond() external onlyGovernor {
  92          Diamond.DiamondStorage storage diamondStorage = Diamond.getDiamondStorage();

ethereum/contracts/zksync/facets/Governance.sol:
   14      /// @param _newPendingGovernor Address of the new governor
   15:     function setPendingGovernor(address _newPendingGovernor) external onlyGovernor {
   16          // Save previous value into the stack to put it into the event later

   44      /// @param _active Active flag
   45:     function setValidator(address _validator, bool _active) external onlyGovernor {
   46          if (s.validators[_validator] != _active) {

   53      /// @param _l2BootloaderBytecodeHash The hash of bootloader L2 bytecode
   54:     function setL2BootloaderBytecodeHash(bytes32 _l2BootloaderBytecodeHash) external onlyGovernor {
   55          L2ContractHelper.validateBytecodeHash(_l2BootloaderBytecodeHash);

   68      /// @param _l2DefaultAccountBytecodeHash The hash of default account L2 bytecode
   69:     function setL2DefaultAccountBytecodeHash(bytes32 _l2DefaultAccountBytecodeHash) external onlyGovernor {
   70          L2ContractHelper.validateBytecodeHash(_l2DefaultAccountBytecodeHash);

   83      /// @param _zkPorterIsAvailable The availability of zk porter shard
   84:     function setPorterAvailability(bool _zkPorterIsAvailable) external onlyGovernor {
   85          if (s.zkPorterIsAvailable != _zkPorterIsAvailable) {

   93      /// @param _newVerifier Verifier smart contract address
   94:     function setVerifier(Verifier _newVerifier) external onlyGovernor {
   95          Verifier oldVerifier = s.verifier;

  103      /// @param _newVerifierParams New parameters for the verifier
  104:     function setVerifierParams(VerifierParams calldata _newVerifierParams) external onlyGovernor {
  105          VerifierParams memory oldVerifierParams = s.verifierParams;
```

### Recommendation;

`governor` can be a Multisign wallet and this part is specified in the documentation.


### [L-03] Incorrect NatSpec in `deposit` function for `_amount`

`_amount` NatSpec comment in `deposit` function; It is the "total amount of ether to bridge". However, the total ether amount to Bridge is msg.value and the _amount value may be less than msg.value

Also, the difference between these two values should be prevented with a require.

```solidity
    /// @param _amount The total amount of ether to be bridged
    /// @return txHash The L2 transaction hash of deposit finalization    
function deposit(
        address _l2Receiver,
        address _l1Token,
        uint256 _amount
    ) external payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 txHash) {
        require(_l1Token == CONVENTIONAL_ETH_ADDRESS, "bx");

        // Will revert if msg.value is less than the amount of the deposit
        uint256 zkSyncFee = msg.value - _amount;
        bytes memory l2TxCalldata = _getDepositL2Calldata(msg.sender, _l2Receiver, _amount);
        txHash = zkSyncMailbox.requestL2Transaction{value: zkSyncFee}(
            l2Bridge,
            0, // L2 msg.value
            l2TxCalldata,
            DEPOSIT_ERGS_LIMIT,
            new bytes[](0)
        );

        // Save the deposit amount, to claim funds back if the L2 transaction will failed
        depositAmount[msg.sender][txHash] = _amount;

        emit DepositInitiated(msg.sender, _l2Receiver, _l1Token, _amount);
    }
```

### [L-04] Require messages are too short and unclear

**Context:**
[L1ERC20Bridge.sol#L77](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L77), [L1ERC20Bridge.sol#L117](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L117), [L1ERC20Bridge.sol#L207](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L207), [L1ERC20Bridge.sol#L210](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L210), [L1ERC20Bridge.sol#L232](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L232), 
[L1ERC20Bridge.sol#L249](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L249), [L1ERC20Bridge.sol#L271](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L271), [L1ERC20Bridge.sol#L274](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L274), [L1EthBridge.sol#L93](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L93), [L1EthBridge.sol#L141](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L141), [L1EthBridge.sol#L166](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L166), [L1EthBridge.sol#L189](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L189), [L1EthBridge.sol#L205](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L205), [AllowListed.sol#L15](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowListed.sol#L15), [AllowList.sol#L33](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L33), [AllowList.sol#L38](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L38), [AllowList.sol#L67](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L67), [AllowList.sol#L100](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L100), [AllowList.sol#L155](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L155), [L2ContractHelper.sol#L50](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L50), [L2ContractHelper.sol#L53-L54](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L53-L54), [L2ContractHelper.sol#L65-LL67](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L65-LL67), [ReentrancyGuard.sol#L55](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/ReentrancyGuard.sol#L55), [ReentrancyGuard.sol#L72](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/ReentrancyGuard.sol#L72), [DiamondProxy.sol#L13](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L13), [DiamondProxy.sol#L24](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L24), [DiamondProxy.sol#L29-LL30](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L29-LL30), [Base.sol#L16](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Base.sol#L16), [Base.sol#L22](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Base.sol#L22), [DiamondCut.sol#L23](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L23), [DiamondCut.sol#L40](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L40), [DiamondCut.sol#L55-L56](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L55-L56), [DiamondCut.sol#L59-L62](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L59-L62), [DiamondCut.sol#L65](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L65), [DiamondCut.sol#L80](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L80), [DiamondCut.sol#L94](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L94), [DiamondCut.sol#L106](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L106), [DiamondCut.sol#L108](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L108), [DiamondCut.sol#L111-L112](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L111-L112), [Executor.sol#L28](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L28), [Executor.sol#L39](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L39), [Executor.sol#L41](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L41), [Executor.sol#L52-L53](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L52-L53), [Executor.sol#L57-L60](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L57-L60), [Executor.sol#L117](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L117), [Executor.sol#L129](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L129), [Executor.sol#L136](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L136), [Executor.sol#L142-L145](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L142-L145), [Executor.sol#L159](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L159), [Executor.sol#L192-L195](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L192-L195), [Executor.sol#L199](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L199), [Executor.sol#L216](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L216), [Executor.sol#L237](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L237), [Executor.sol#L241-L244](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L241-L244), [Executor.sol#L261](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L261), [Executor.sol#L265](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L265), [Executor.sol#L268](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L268), [Executor.sol#L337](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L337), [Getters.sol#L148](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Getters.sol#L148), [Governance.sol#L30](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Governance.sol#L30), [Mailbox.sol#L56](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L56), [Mailbox.sol#L63](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L63), [Mailbox.sol#L66](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L66), [Mailbox.sol#L124](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L124), [Diamond.sol#L100](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L100), [Diamond.sol#L126](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L126), [Diamond.sol#L135](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L135), [Diamond.sol#L150](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L150), [Diamond.sol#L156](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L156), [Diamond.sol#L170](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L170), [Diamond.sol#L176](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L176), [Diamond.sol#L214](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L214), [Diamond.sol#L279](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L279), [Diamond.sol#L283](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L283), [Diamond.sol#L287-L288](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L287-L288), [Merkle.sol#L23-L25](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol#L23-L25), [PriorityQueue.sol#L64](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L64), [PriorityQueue.sol#L72](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L72)

**Description:**
The correct and clear error description explains to the user why the function reverts, but the error descriptions below in the project are not self-explanatory. These error descriptions are very important in the debug features of DApps like Tenderly. Error definitions should be added to the require block, not exceeding 32 bytes.


### [L-05] If the `_amount` sent to L2 is lower than the minimum tx value, the user cannot use the fund in L2


https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L88-L110

The 'deposit' function of the L1EthBridge.sol contract sends Ether to L2 and there is no limit amount.
However, sending very small amounts of tokens will cause the balance to be unused in L2.
This is a common problem in Layer2s for reasons such as airdrop, etc.
For example, sending an amount of 10 wei will cause this.

1- Alice sends 20 wei from L1 to L2
2- With this amount, nothing can be done to L2 and dust remains.

### Recommended Mitigation Steps
Set the min sending amount, Ex: 21000 wei


### [N-01]  `_withdrawFunds` function create dirty bits

This explanation should be added in the NatSpec comments of this function that sends ether with call;

Note that this code probably isn’t secure or a good use case for assembly because a lot of memory management and security checks are bypassed.
Use with caution! Some functions in this contract knowingly create dirty bits at the destination of the free memory pointer.


```solidity
ethereum/contracts/bridge/L1EthBridge.sol:
  231      /// @dev Reverts only if the transfer call failed
  232:     function _withdrawFunds(address _to, uint256 _amount) internal {
  233:         bool callSuccess;
  234:         // Low-level assembly call, to avoid any memory copying (save gas)
  235:         assembly {
  236:             callSuccess := call(gas(), _to, _amount, 0, 0, 0, 0)
  237:         }
  238:         require(callSuccess);
  239:     }
```

Recommendation:
Add this comment to `_withdrawFunds`function;
/// @dev Use with caution! Some functions in this contract knowingly create dirty bits at the destination of the free memory pointer. Note that this code probably isn’t secure or a good use case for assembly because a lot of memory management and security checks are bypassed.

### [N-02] Insufficient coverage

**Description:**
Testing all functions is best practice in terms of security criteria.

```js
-----------------------------------------|----------|----------|----------|----------|----------------|
File                                     |  % Stmts | % Branch |  % Funcs |  % Lines |Uncovered Lines |
-----------------------------------------|----------|----------|----------|----------|----------------|
 bridge/                                 |        0 |        0 |        0 |        0 |                |
  L1ERC20Bridge.sol                      |        0 |        0 |        0 |        0 |... 283,284,286 |
  L1EthBridge.sol                        |        0 |        0 |        0 |        0 |... 235,238,244 |
 common/                                 |        0 |        0 |        0 |        0 |                |
  AllowList.sol                          |        0 |        0 |        0 |        0 |... 159,161,162 |
  AllowListed.sol                        |        0 |        0 |        0 |        0 |    11,14,15,17 |
  L2ContractHelper.sol                   |        0 |        0 |        0 |        0 |... 72,81,82,86 |
  ReentrancyGuard.sol                    |        0 |        0 |        0 |        0 |... 72,75,79,83 |
 zksync/                                 |        0 |        0 |        0 |        0 |                |
  DiamondInit.sol                        |        0 |        0 |        0 |        0 |... 57,58,59,61 |
  DiamondProxy.sol                       |        0 |        0 |        0 |        0 |... 27,29,30,32 |
  Verifier.sol                           |        0 |        0 |        0 |        0 |... 231,233,235 |
 zksync/facets/                          |        0 |        0 |        0 |        0 |                |
  Base.sol                               |        0 |        0 |        0 |        0 |    16,17,22,23 |
  DiamondCut.sol                         |        0 |        0 |        0 |        0 |... 132,133,135 |
  Executor.sol                           |        0 |        0 |        0 |        0 |... 379,381,386 |
  Getters.sol                            |        0 |        0 |        0 |        0 |... 180,185,186 |
  Governance.sol                         |        0 |        0 |        0 |        0 |... 105,107,108 |
 zksync/libraries/                       |        0 |        0 |        0 |        0 |                |
  Diamond.sol                            |        0 |        0 |        0 |        0 |... 283,287,288 |
  Merkle.sol                             |        0 |        0 |        0 |        0 |... 30,32,34,37 |
  PriorityQueue.sol                      |        0 |        0 |        0 |        0 |... 75,77,78,79 |
-----------------------------------------|----------|----------|----------|----------|----------------|
```

Due to its capacity, test coverage is expected to be 100%


### [N-03]  Using Vulnerable Version of Openzeppelin
The package.json configuration file says that the project is using 4.6.0 of OpenZeppelin which has a vulnerability in initializers that call external contracts.

openzeppelin/contracts vulnerabilities:
https://security.snyk.io/package/npm/@openzeppelin%2Fcontracts/


There is 2 instance of this issue

```js
zksync/package.json:
  1  {
  2:   "name": "l2-zksync-contracts",
  3:   "version": "0.1.0",
  4:   "license": "MIT",
  5:   "devDependencies": {
  8:     "@openzeppelin/contracts": "4.6.0",
  9:     "@openzeppelin/contracts-upgradeable": "^4.6.0",

ethereum/package.json:
   1  {
   2:   "name": "l1-zksync-contracts",
   3:   "version": "0.1.0",
   4:   "license": "MIT",
   5:   "devDependencies": {
   9:     "@openzeppelin/contracts": "4.6.0",
  10:     "@openzeppelin/contracts-upgradeable": "4.6.0",
```

**Recommendation:**
Use patched versions


### [N-04] `0 address` check

**Context:**

```solidity
ethereum/contracts/bridge/L1ERC20Bridge.sol:
   56      /// @dev Initialize the implementation to prevent Parity hack.
   57:     constructor(IMailbox _mailbox, IAllowList _allowList) reentrancyGuardInitializer {
   58          zkSyncMailbox = _mailbox;
   59          allowList = _allowList;

ethereum/contracts/bridge/L1EthBridge.sol:
  47      /// @dev Initialize the implementation to prevent Parity hack.
  48:     constructor(IMailbox _mailbox, IAllowList _allowList) reentrancyGuardInitializer {
  49          zkSyncMailbox = _mailbox;
  59          allowList = _allowList;
```

**Description:**
Also check of the address to protect the code from 0x0 address  problem just in case. This is best practice or instead of suggesting that they verify address != 0x0, you could add some good NatSpec comments explaining what is valid and what is invalid and what are the implications of accidentally using an invalid address.

**Recommendation:**
like this;
`if (oracle == address(0)) revert ADDRESS_ZERO();`


### [N-05] `Function writing` that does not comply with the `Solidity Style Guide`

**Context:**
All Contracts

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last

### [N-06] Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters

The events should include the new value and old value where possible:

Events with no old value;

```solidity
ethereum/contracts/common/AllowList.sol:
162:             emit NewOwner(newOwner);
```

### [N-07] Add parameter to Event-Emit

Some event-emit description hasn’t parameter. Add to parameter  for front-end website or client app , they can has that something has happened on the blockchain.

Events with no old value;

```solidity
2 results - 2 files

ethereum/contracts/zksync/facets/DiamondCut.sol:
  86  
  87:         emit EmergencyFreeze();
  88      }

ethereum/contracts/zksync/interfaces/IDiamondCut.sol:
  25  
  26:     event EmergencyFreeze();
  27 
```

### [N-08] Use a more recent version of Solidity

**Context:**
All contracts

**Description:**
For security, it is best practice to use the latest Solidity version.
For the security fix list in the versions;
https://github.com/ethereum/solidity/blob/develop/Changelog.md


**Recommendation:**
Old version of Solidity is used `(^0.8.0)`, newer version can be used `(0.8.17)` 

### [N-09] Solidity compiler optimizations can be problematic

```js
ethereum/hardhat.config.ts:
  20  
  21: export default {
  22:     solidity: {
  23:         version: '0.8.17',
  24:         settings: {
  25:             optimizer: {
  26:                 enabled: true,
  27:                 runs: 200
  28:             },
  29:             outputSelection: {
  30:                 '*': {
  31:                     '*': ['storageLayout']
  32:                 }
  33:             }
  34:         }
  35:     },
```

**Description:**
Protocol has enabled optional compiler optimizations in Solidity.
There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. 

Therefore, it is unclear how well they are being tested and exercised.
High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. 

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported.
A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe.
It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario
A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.


**Recommendation:**
Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug.
Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.


### [N-10] NatSpec is missing 

**Description:**
NatSpec is missing for the following functions , constructor and modifier:

**Context:**

[L2ContractHelper.sol](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol)

### [N-11] Require revert cause should be known

**Context:**

```solidity
7 results - 2 files

ethereum/contracts/bridge/L1EthBridge.sol:

  144          uint256 amount = depositAmount[_depositSender][_l2TxHash];
  145:         require(amount != 0);
  146   

  220          // It should be equal to the length of the function signature + address + uint256 = 4 + 20 + 32 = 56 (bytes).
  221:         require(_message.length == 56);
  222  
  223          (uint32 functionSignature, uint256 offset) = UnsafeBytes.readUint32(_message, 0);
  224:         require(bytes4(functionSignature) == this.finalizeWithdrawal.selector);
  225  

  237          }
  238:         require(callSuccess);
  239      }

ethereum/contracts/zksync/facets/Executor.sol:
   
   42          // Check that the number of processed priority operations is as expected
   43:         require(expectedNumberOfLayer1Txs == _newBlock.numberOfLayer1Txs);
   44          // Check that the timestamp that came from the Bootloader is expected
   45:         require(l2BlockTimestamp == _newBlock.timestamp);
   46  

  296      function _verifyRecursivePartOfProof(uint256[] calldata _recurisiveAggregationInput) internal view returns (bool) {
  297:         require(_recurisiveAggregationInput.length == 4);
  298
```
**Description:**
Vulnerability related to description is not written to require, it must be written so that the user knows why the process is reverted.

This is an important debug analysis for programs and users that analyze transaction revert details such as Tenderly.

**Recommendation:**

```solidity
ethereum/contracts/bridge/L1EthBridge.sol:

 144          uint256 amount = depositAmount[_depositSender][_l2TxHash];
 145:         require(amount != 0);
 146   

145:         require(amount != 0, "Amount can be deposited");
```

### [N-12]  Open TODOs

**Context:**
```solidity
5 results - 3 files

ethereum/contracts/zksync/Config.sol:
  27  
  28: // TODO: change constant to the real root hash of empty Merkle tree (SMA-184)
  29  bytes32 constant DEFAULT_L2_LOGS_TREE_ROOT_HASH = bytes32(0);


ethereum/contracts/zksync/facets/Mailbox.sol:
   94:         // TODO: estimate gas for L1 execute
   95          return 0;

  126          uint256 txId = s.priorityQueue.getTotalPriorityTxs();
  127:         // TODO: Restore after stable priority op fee modeling. (SMA-1230)
  128          // uint256 baseCost = l2TransactionBaseCost(tx.gasprice, _ergsLimit, uint32(_calldata.length));

  168                  expirationBlock: _expirationBlock,
  169:                 layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)
  170              })

ethereum/contracts/zksync/interfaces/IExecutor.sol:
  55      /// @notice Recursive proof input data (individual commitments are constructed onchain)
  56:     /// TODO: The verifier integration is not finished yet, change the structure for compatibility later
  57      struct ProofInput {
```

**Recommendation:**
Use temporary TODOs as you work on a feature, but make sure to treat them before merging. Either add a link to a proper issue in your TODO, or remove it from the code.


### [N-13] For modern and more readable code; update import usages

**Context:**
All contracts

**Description:**
Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct `polluted the source code` with an unnecessary object we were not using because we did not need it. 
This was breaking the rule of modularity and modular programming: `only import what you need` Specific imports with curly braces allow us to apply this rule better.

**Recommendation:**
`import {contract1 , contract2} from "filename.sol";`

A good example from the ArtGobblers project;
```js
import {Owned} from "solmate/auth/Owned.sol";
import {ERC721} from "solmate/tokens/ERC721.sol";
import {LibString} from "solmate/utils/LibString.sol";
import {MerkleProofLib} from "solmate/utils/MerkleProofLib.sol";
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
import {ERC1155, ERC1155TokenReceiver} from "solmate/tokens/ERC1155.sol";
import {toWadUnsafe, toDaysWadUnsafe} from "solmate/utils/SignedWadMath.sol";
```


### [N-14] Use `require` instead of `assert`

```solidity
ethereum/contracts/zksync/facets/DiamondCut.sol:
  11  /// @author Matter Labs
  12: contract DiamondCutFacet is Base, IDiamondCut {
  13:     constructor() {
  14:         // Caution check for config value.
  15:         // Should be greater than 0, otherwise zero approvals will be enough to make an instant upgrade!
  16:         assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);
  17:     }
```
**Description:**
Assert should not be used except for tests, `require` should be used

Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process's available gas instead of returning it, as request()/revert() did.

assert() and ruqire();
The big difference between the two is that the `assert()`function when false, uses up all the remaining gas and reverts all the changes made.
Meanwhile, a  `require()` function when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay. This is the most common Solidity function used by developers for debugging and error handling.

Assertion() should be avoided even after solidity version 0.8.0, because its documentation states "The Assert function generates an error of type Panic(uint256). Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. there's a mistake".






### [S-01] Generate perfect code headers every time

**Description:**
I recommend using header for Solidity code layout and readability

https://github.com/transmissions11/headers

```js
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```
### [S-02] Add NatSpec comments to the variables defined in Storage

**Description:**
I recommend adding NatSpec comments explaining the variables defined in Storage, their slots, their contents and definitions.

This improves code readability and control quality


Current Code;
```solidity
ethereum/contracts/bridge/L1EthBridge.sol:
  23  
  24:     /// @dev Ergs limit for requesting L2 deposit finalization transaction
  25:     /// NOTE: constant is not calculated accurately, because ergs count in L2 is not stable yet
  26:     uint256 constant DEPOSIT_ERGS_LIMIT = 2097152;
  27: 
  28:     /// @dev Ergs limit for requesting L1 -> L2 transaction of deploying L2 bridge instance
  29:     /// NOTE: constant is not calculated accurately, because ergs count in L2 is not stable yet
  30:     uint256 constant DEPLOY_L2_BRIDGE_COUNTERPART_ERGS_LIMIT = 2097152;
  31: 
  32:     /// @dev Ether native coin has no real address on L1, so a conventional zero address is used.
  33:     address constant CONVENTIONAL_ETH_ADDRESS = address(0);
  34: 
  35:     /// @dev A mapping L2 block number => message number => flag
  36:     /// @dev Used to indicate that zkSync L2 -> L1 message was already processed
  37:     mapping(uint256 => mapping(uint256 => bool)) public isWithdrawalFinalized;
  38: 
  39:     /// @dev A mapping account => L1 token address => L2 deposit transaction hash => amount
  40:     /// @dev Used for saving the number of deposited funds, to claim them in case the deposit transaction will fail
  41:     mapping(address => mapping(bytes32 => uint256)) depositAmount;
  42: 
  43:     /// @dev The address of deployed L2 bridge counterpart
  44:     address public l2Bridge;
```

Recommendation Code;

```solidity

 //****** Slot 0 ******//

 24:     /// @dev Ergs limit for requesting L2 deposit finalization transaction
 25:     /// NOTE: constant is not calculated accurately, because ergs count in L2 is not stable yet
 26:     uint256 constant DEPOSIT_ERGS_LIMIT = 2097152;

 / /****** Slot 1 ******//
 28:     /// @dev Ergs limit for requesting L1 -> L2 transaction of deploying L2 bridge instance
 29:     /// NOTE: constant is not calculated accurately, because ergs count in L2 is not stable yet
 30:     uint256 constant DEPLOY_L2_BRIDGE_COUNTERPART_ERGS_LIMIT = 2097152;


  //****** Slot 2 ******//
 32:     /// @dev Ether native coin has no real address on L1, so a conventional zero address is used.
 33:     address constant CONVENTIONAL_ETH_ADDRESS = address(0);

...

  /****** End of storage ******/
```