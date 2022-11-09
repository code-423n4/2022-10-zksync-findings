# \[L-01\] Improving State Variables' Visibility For Transparency And Traceability

## Vulnerability Details

We found **some state variables are declared with *internal* visibility, which could raise transparency and traceability issues**.

The following lists all state variables with *internal* visibility.

1.  `AppStorage internal s;` (**L12 in code snippet 1**)
2.  `IAllowList immutable allowList;` (**L25 in code snippet 2**)
3.  `IMailbox immutable zkSyncMailbox;` (**L28 in code snippet 2**)
4.  `mapping(address => mapping(address => mapping(bytes32 => uint256))) depositAmount;` (**L44 in code snippet 2**)
5.  `IAllowList immutable allowList;` (**L19 in code snippet 3**)
6.  `IMailbox immutable zkSyncMailbox;` (**L22 in code snippet 3**)
7.  `mapping(address => mapping(bytes32 => uint256)) depositAmount;` (**L41 in code snippet 3**)
8.  `bytes32 l2TokenProxyBytecodeHash;` (**L26 in code snippet 4**)

```solidity
SNIPPET: 1
FILE: https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Base.sol

11:     contract Base is ReentrancyGuard, AllowListed {
12:         AppStorage internal s;

            // (...SNIPPED...)
25:     }
```

```solidity
SNIPPET: 2
FILE: https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol

21:     contract L1ERC20Bridge is IL1Bridge, AllowListed, ReentrancyGuard {
            // (...SNIPPED...)

24:         /// @dev The smart contract that manages the list with permission to call contract functions
25:         IAllowList immutable allowList;
26: 
27:         /// @dev zkSync smart contract that used to operate with L2 via asynchronous L2 <-> L1 communication
28:         IMailbox immutable zkSyncMailbox;

            // (...SNIPPED...)

44:         mapping(address => mapping(address => mapping(bytes32 => uint256))) depositAmount;

            // (...SNIPPED...)
288:    }
```

```solidity
SNIPPET: 3
FILE: https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol

17:     contract L1EthBridge is IL1Bridge, AllowListed, ReentrancyGuard {
18:         /// @dev The smart contract that manages the list with permission to call contract functions
19:         IAllowList immutable allowList;
20: 
21:         /// @dev zkSync smart contract used to interact with L2 via asynchronous L2 <-> L1 communication
22:         IMailbox immutable zkSyncMailbox;

            // (...SNIPPED...)

41:         mapping(address => mapping(bytes32 => uint256)) depositAmount;

            // (...SNIPPED...)
246:    }
```

```solidity
SNIPPET: 4
FILE: https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol

17:     contract L2ERC20Bridge is IL2Bridge {
            // (...SNIPPED...)

26:         bytes32 l2TokenProxyBytecodeHash;

            // (...SNIPPED...)
125:    }
```

## Impact

**State variables with *internal* visibility could raise transparency and traceability issues.**

## Proof of Concept

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Base.sol#L12

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L25

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L28

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L44

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L19

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L22

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L41

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L26

## Tools Used

VSCode (Manual Review)

## Recommended Mitigation Steps

We recommend **changing the associated state variables' visibility to `public`** to improve transparency and traceability.



---



# \[L-02\] Improving require() and revert() Statements For Better Log Traceability

## Vulnerability Details

We found **some `require()` and `revert()` statements are applied without specifying error messages, which could impede the log traceability of users and developers when they revert transactions**.

The following lists all `require()` and `revert()` statements that do not specify error messages.

1.  **L145 in ./ethereum/contracts/bridge/L1EthBridge.sol**
2.  **L221 in ./ethereum/contracts/bridge/L1EthBridge.sol**
3.  **L224 in ./ethereum/contracts/bridge/L1EthBridge.sol**
4.  **L238 in ./ethereum/contracts/bridge/L1EthBridge.sol**
5.  **L50 in ./zksync/contracts/bridge/L2ETHBridge.sol**
6.  **L96 in ./zksync/contracts/bridge/L2StandardERC20.sol**
7.  **L116 in ./zksync/contracts/bridge/L2StandardERC20.sol**
8.  **L122 in ./zksync/contracts/bridge/L2StandardERC20.sol**
9.  **L128 in ./zksync/contracts/bridge/L2StandardERC20.sol**

## Impact

**The `require()` and `revert()` statements that do not specify error messages could impede the log traceability of users and developers when they revert transactions.**

## Proof of Concept

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L145

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L221

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L224

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L238

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L50

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L96

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L116

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L122

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L128

## Tools Used

VSCode (Manual Review)

## Recommended Mitigation Steps

We recommend **specifying proper error messages to the associated `require()` and `revert()` statements** to improve the log traceability of users and developers.



---



# \[L-03\] Incorrect Event Emission

## Vulnerability Details

We noticed that **the event `EmergencyDiamondCutApproved` would emit the outdated approval amount, `securityCouncilEmergencyApprovals`** (L119 in code snippet 1).

We considered that **emitting incorrect log events could impede the log traceability of admins who maintain the zkSync platform**.

```solidity
SNIPPET: 1
FILE: https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol

105:    function approveEmergencyDiamondCutAsSecurityCouncilMember(bytes32 _diamondCutHash) external {
106:        require(s.diamondCutStorage.securityCouncilMembers[msg.sender], "a9"); // not a security council member
107:        uint256 currentProposalId = s.diamondCutStorage.currentProposalId;
108:        require(s.diamondCutStorage.securityCouncilMemberLastApprovedProposalId[msg.sender] < currentProposalId, "ao"); // already approved this proposal
109:        s.diamondCutStorage.securityCouncilMemberLastApprovedProposalId[msg.sender] = currentProposalId;
110:
111:        require(s.diamondCutStorage.proposedDiamondCutTimestamp != 0, "f0"); // there is no proposed diamond cut
112:        require(s.diamondCutStorage.proposedDiamondCutHash == _diamondCutHash, "f1"); // proposed diamond cut do not match to the approved
113:        uint256 securityCouncilEmergencyApprovals = s.diamondCutStorage.securityCouncilEmergencyApprovals;
114:        s.diamondCutStorage.securityCouncilEmergencyApprovals = securityCouncilEmergencyApprovals + 1;
115:
116:        emit EmergencyDiamondCutApproved(
117:            msg.sender,
118:            currentProposalId,
119:            securityCouncilEmergencyApprovals,
120:            _diamondCutHash
121:        );
122:    }
```

## Impact

We considered that **emitting incorrect log events could impede the log traceability of admins who maintain the zkSync platform**.

## Proof of Concept

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L119

## Tools Used

VSCode (Manual Review)

## Recommended Mitigation Steps

We recommend **updating the event `EmergencyDiamondCutApproved` by emitting the up-to-date approval amount `s.diamondCutStorage.securityCouncilEmergencyApprovals`** like L119 in code snippet 2.

```solidity
SNIPPET: 2
FILE: https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol

105:    function approveEmergencyDiamondCutAsSecurityCouncilMember(bytes32 _diamondCutHash) external {
106:        require(s.diamondCutStorage.securityCouncilMembers[msg.sender], "a9"); // not a security council member
107:        uint256 currentProposalId = s.diamondCutStorage.currentProposalId;
108:        require(s.diamondCutStorage.securityCouncilMemberLastApprovedProposalId[msg.sender] < currentProposalId, "ao"); // already approved this proposal
109:        s.diamondCutStorage.securityCouncilMemberLastApprovedProposalId[msg.sender] = currentProposalId;
110:
111:        require(s.diamondCutStorage.proposedDiamondCutTimestamp != 0, "f0"); // there is no proposed diamond cut
112:        require(s.diamondCutStorage.proposedDiamondCutHash == _diamondCutHash, "f1"); // proposed diamond cut do not match to the approved
113:        uint256 securityCouncilEmergencyApprovals = s.diamondCutStorage.securityCouncilEmergencyApprovals;
114:        s.diamondCutStorage.securityCouncilEmergencyApprovals = securityCouncilEmergencyApprovals + 1;
115:
116:        emit EmergencyDiamondCutApproved(
117:            msg.sender,
118:            currentProposalId,
119:            s.diamondCutStorage.securityCouncilEmergencyApprovals,
120:            _diamondCutHash
121:        );
122:    }
```