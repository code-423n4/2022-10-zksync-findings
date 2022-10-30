## Require with empty message

The following requires are with empty messages. 
This is very important to add a message for any require. So the user has enough information to know the reason of failure.
### Code instances:

        Solidity file: Plonk4VerifierWithAccessToDNext.sol, In line 116 with Empty Require message.
        Solidity file: Plonk4VerifierWithAccessToDNext.sol, In line 208 with Empty Require message.
        Solidity file: PairingsBn254.sol, In line 20 with Empty Require message.
        Solidity file: PairingsBn254.sol, In line 33 with Empty Require message.
        Solidity file: L2ETHBridge.sol, In line 50 with Empty Require message.
        Solidity file: Plonk4VerifierWithAccessToDNext.sol, In line 111 with Empty Require message.
        Solidity file: PairingsBn254.sol, In line 257 with Empty Require message.
        Solidity file: Plonk4VerifierWithAccessToDNext.sol, In line 323 with Empty Require message.
        Solidity file: Plonk4VerifierWithAccessToDNext.sol, In line 224 with Empty Require message.
        Solidity file: Plonk4VerifierWithAccessToDNext.sol, In line 248 with Empty Require message.
        Solidity file: Plonk4VerifierWithAccessToDNext.sol, In line 620 with Empty Require message.
        Solidity file: PairingsBn254.sol, In line 233 with Empty Require message.
        Solidity file: Plonk4VerifierWithAccessToDNext.sol, In line 445 with Empty Require message.
        Solidity file: Verifier.sol, In line 231 with Empty Require message.
        Solidity file: Plonk4VerifierWithAccessToDNext.sol, In line 487 with Empty Require message.
        Solidity file: PairingsBn254.sol, In line 56 with Empty Require message.
        Solidity file: PairingsBn254.sol, In line 237 with Empty Require message.



## Require with not comprehensive message

The following requires has a non comprehensive messages. 
This is very important to add a comprehensive message for any require. Such that the user has enough 
information to know the reason of failure: 

### Code instances:

        Solidity file: L2StandardERC20.sol, In line 44 with Require message: in5
        Solidity file: L2ContractHelper.sol, In line 67 with Require message: uy
        Solidity file: PriorityQueue.sol, In line 72 with Require message: s
        Solidity file: DiamondCut.sol, In line 59 with Require message: a4
        Solidity file: DiamondCut.sol, In line 108 with Require message: ao
        Solidity file: DiamondProxy.sol, In line 24 with Require message: Ut
        Solidity file: L2ETHBridge.sol, In line 49 with Require message: ni
        Solidity file: L2ContractHelper.sol, In line 53 with Require message: pp
        Solidity file: L2ContractHelper.sol, In line 54 with Require message: pr
        Solidity file: DiamondCut.sol, In line 56 with Require message: f3
        Solidity file: AllowList.sol, In line 96 with Require message: yw
        Solidity file: DiamondProxy.sol, In line 13 with Require message: pr
        Solidity file: DiamondCut.sol, In line 23 with Require message: a3
        Solidity file: PriorityQueue.sol, In line 64 with Require message: D
        Solidity file: AllowList.sol, In line 67 with Require message: yg
        Solidity file: DiamondProxy.sol, In line 29 with Require message: F
        Solidity file: L2ContractHelper.sol, In line 50 with Require message: po
        Solidity file: ReentrancyGuard.sol, In line 55 with Require message: 1B
        Solidity file: L2ERC20Bridge.sol, In line 58 with Require message: mq
        Solidity file: Getters.sol, In line 148 with Require message: g2
        Solidity file: Mailbox.sol, In line 124 with Require message: ui
        Solidity file: DiamondCut.sol, In line 80 with Require message: a9
        Solidity file: AllowList.sol, In line 33 with Require message: kq
        Solidity file: DiamondCut.sol, In line 55 with Require message: a6
        Solidity file: DiamondCut.sol, In line 112 with Require message: f1
        Solidity file: Merkle.sol, In line 24 with Require message: bt
        Solidity file: DiamondCut.sol, In line 106 with Require message: a9
        Solidity file: DiamondProxy.sol, In line 30 with Require message: q1
        Solidity file: Merkle.sol, In line 25 with Require message: pz
        Solidity file: DiamondCut.sol, In line 40 with Require message: g1
        Solidity file: Governance.sol, In line 30 with Require message: n4
        Solidity file: Mailbox.sol, In line 63 with Require message: tw
        Solidity file: Mailbox.sol, In line 66 with Require message: rz
        Solidity file: DiamondCut.sol, In line 65 with Require message: a5
        Solidity file: L2ERC20Bridge.sol, In line 95 with Require message: yh
        Solidity file: L2ETHBridge.sol, In line 64 with Require message: zn
        Solidity file: AllowList.sol, In line 155 with Require message: n0
        Solidity file: DiamondCut.sol, In line 111 with Require message: f0
        Solidity file: L2ContractHelper.sol, In line 65 with Require message: zf
        Solidity file: DiamondCut.sol, In line 94 with Require message: a7
        Solidity file: Merkle.sol, In line 23 with Require message: xc
        Solidity file: Mailbox.sol, In line 56 with Require message: xx
        Solidity file: L2StandardERC20.sol, In line 45 with Require message: in6



## Not verified input


external / public functions parameters should be validated to make sure the address is not 0.
Otherwise if not given the right input it can mistakenly lead to loss of user funds.    

### Code instances:

        L2ERC20Bridge.sol.finalizeDeposit _l1Sender
        DiamondInit.sol.initialize _validator
        L2ERC20Bridge.sol.withdraw _l1Receiver
        Mailbox.sol.requestL2Transaction _contractL2
        Governance.sol.setValidator _validator
        L2ETHBridge.sol.finalizeDeposit _l1Sender
        AllowList.sol.setPermissionToCall _caller
        L2ERC20Bridge.sol.finalizeDeposit _l1Token
        TestnetERC20Token.sol.mint _to
        L2ERC20Bridge.sol.finalizeDeposit _l2Receiver
        L2ERC20Bridge.sol.withdraw _l2Token
        Governance.sol.setPendingGovernor _newPendingGovernor
        L2ETHBridge.sol.finalizeDeposit _l2Receiver
        AllowList.sol.setPermissionToCall _target
        L2ETHBridge.sol.withdraw _l1Receiver
        DiamondCut.sol.proposeDiamondCut _initAddress
        AllowList.sol.setPublicAccess _target
        DiamondInit.sol.initialize _governor
        L2StandardERC20.sol.bridgeMint _to
        L2StandardERC20.sol.bridgeInitialize _l1Address
        L2StandardERC20.sol.bridgeBurn _from



## Solidity compiler versions mismatch


The project is compiled with different versions of solidity, which is not recommended because it can lead to undefined behaviors.
        
### Code instance:

        



## Not verified owner


        owner param should be validated to make sure the owner address is not address(0).
        Otherwise if not given the right input all only owner accessible functions will be unaccessible.
        
        
### Code instance:

        AllowList.sol.setPendingOwner _newPendingOwner



## Init frontrun

Most contracts use an init pattern (instead of a constructor) to initialize contract parameters. Unless these are enforced to be atomic with contact deployment via deployment script or factory contracts, they are susceptible to front-running race conditions where an attacker/griefer can front-run (cannot access control because admin roles are not initialized) to initially with their own (malicious) parameters upon detecting (if an event is emitted) which the contract deployer has to redeploy wasting gas and risking other transactions from interacting with the attacker-initialized contract.

Many init functions do not have an explicit event emission which makes monitoring such scenarios harder. All of them have re-init checks; while many are explicit some (those in auction contracts) have implicit reinit checks in initAccessControls() which is better if converted to an explicit check in the main init function itself.
(details credit to: https://github.com/code-423n4/2021-09-sushimiso-findings/issues/64)
The vulnerable initialization functions in the codebase are: 

### Code instances:

        L2StandardERC20.sol, bridgeInitialize, 43
        L2StandardERC20.sol, constructor, 39



## Named return issue

Users can mistakenly think that the return value is the named return, but it is actually the actualreturn statement that comes after. To know that the user needs to read the code and is confusing.
Furthermore, removing either the actual return or the named return will save gas. 

### Code instances:

        PairingsBn254.sol, point_mul
        PairingsBn254.sol, point_add



## Assert instead require to validate user inputs


        From solidity docs: Properly functioning code should never reach a failing assert statement; if this happens there is a bug in your contract which you should fix.
        With assert the user pays the gas and with require it doesn't. The ETH network gas isn't cheap and users can see it as a scam.
        
### Code instance:

        DiamondCut.sol : reachable assert in line 15



## Open TODOs

Open TODOs can hint at programming or architectural errors that still need to be fixed. 
These files has open TODOs:

### Code instances:

Open TODO in Plonk4VerifierWithAccessToDNext.sol line 303 :         // require(vk.num_inputs > 0); // TODO

Open TODO in Plonk4VerifierWithAccessToDNext.sol line 484 :         // TODO

Open TODO in PairingsBn254.sol line 207 :         // TODO

Open TODO in Plonk4VerifierWithAccessToDNext.sol line 307 :             // TODO we may use batched lagrange compputation

Open TODO in Mailbox.sol line 126 :         // TODO: Restore after stable priority op fee modeling. (SMA-1230)

Open TODO in Mailbox.sol line 93 :         // TODO: estimate gas for L1 execute

Open TODO in Verifier.sol line 131 :         // require(serialized_proof.length == 44); TODO

Open TODO in Plonk4VerifierWithAccessToDNext.sol line 453 :                 t.mul_assign(vk.non_residues[i - 1]); // TODO add one into non-residues during codegen?

Open TODO in Plonk4VerifierWithAccessToDNext.sol line 42 :         PairingsBn254.Fr[1] state_polys_openings_at_z_omega; // TODO: not use array while there is only D_next

Open TODO in Plonk4VerifierWithAccessToDNext.sol line 223 :         require(proof.state_polys_openings_at_z_omega.length == 1); // TODO

Open TODO in IExecutor.sol line 55 :     /// TODO: The verifier integration is not finished yet, change the structure for compatibility later

Open TODO in Mailbox.sol line 168 :                 layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)




## Missing commenting


        The following functions are missing commenting as describe below:
        
### Code instances:

        DiamondCut.sol, executeDiamondCutProposal (external), parameter _diamondCut not commented
        AllowList.sol, _setPermissionToCall (internal), parameters _caller, _target, _functionSig, _enable not commented
        AllowList.sol, _setPublicAccess (internal), parameters _target, _enable not commented



## Add a timelock

To give more trust to users: functions that set key/critical variables should be put behind a timelock.

### Code instances:

        https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L45
        https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L54
        https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L84
        https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L94
        https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L57
        https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L89
        https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L65
        https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L69
        https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L104
        https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L113
        https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L140
        https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L15


