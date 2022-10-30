## Unnecessary equals boolean


Boolean variables can be checked within conditionals directly without the use of equality operators to true/false.

### Code instance:

        Plonk4VerifierWithAccessToDNext.sol, 221: if (verify_quotient_evaluation(vk, proof, state) == false) {



## State variables that could be set immutable

In the following files there are state variables that could be set immutable to save gas. 

### Code instances:

        l1Bridge in L2ETHBridge.sol
        decimals_ in L2StandardERC20.sol
        l2Bridge in L2StandardERC20.sol
        availableGetters in L2StandardERC20.sol



## Unused state variables

Unused state variables are gas consuming at deployment (since they are located in storage) and are 
a bad code practice. Removing those variables will decrease deployment gas cost and improve code quality. 
This is a full list of all the unused storage variables we found in your code base. 

### Code instances:

        TranscriptLib.sol, FR_MASK
        L2ETHBridge.sol, totalSupply
        L2ETHBridge.sol, balanceOf



## Unused declared local variables

Unused local variables are gas consuming, since the initial value assignment costs gas. And are 
a bad code practice. Removing those variables will decrease the gas cost and improve code quality. 
This is a full list of all the unused storage variables we found in your code base. 

### Code instances:

        L2ERC20Bridge.sol, _deployL2Token, salt
        TranscriptLib.sol, get_challenge, query
        Plonk4VerifierWithAccessToDNext.sol, final_pairing, gen2



## Unnecessary array boundaries check when loading an array element twice


    There are places in the code (especially in for-each loops) that loads the same array element more than once. 
    In such cases, only one array boundaries check should take place, and the rest are unnecessary.
    Therefore, this array element should be cached in a local variable and then be loaded
    again using this local variable, skipping the redundant second array boundaries check: 
    
### Code instance:

        Verifier.sol.deserialize_proof - double load of serialized_proof[j]



## Caching array length can save gas


Caching the array length is more gas efficient.
This is because access to a local variable in solidity is more efficient than query storage / calldata / memory.
We recommend to change from:    

    for (uint256 i=0; i<array.length; i++) { ... }

to: 

    uint len = array.length  
    for (uint256 i=0; i<len; i++) { ... }


### Code instances:

        Plonk4VerifierWithAccessToDNext.sol, commitments_at_z.queries, 255
        Verifier.sol, copy_permutation_polys_openings_at_z.proof, 188
        Plonk4VerifierWithAccessToDNext.sol, commitments_at_z_omega.queries, 272
        Plonk4VerifierWithAccessToDNext.sol, lagrange_poly_numbers, 301
        Verifier.sol, quotient_poly_parts_commitments.proof, 164
        Plonk4VerifierWithAccessToDNext.sol, quotient_poly_parts_commitments.proof, 164
        Verifier.sol, state_polys_openings_at_z.proof, 172
        Verifier.sol, gate_selectors_openings_at_z.proof, 183
        Verifier.sol, public_inputs, 134
        Plonk4VerifierWithAccessToDNext.sol, copy_permutation_polys_openings_at_z.proof, 181
        Plonk4VerifierWithAccessToDNext.sol, state_polys_openings_at_z.proof, 449
        Verifier.sol, state_polys_openings_at_z_omega.proof, 178
        Plonk4VerifierWithAccessToDNext.sol, lookup_tables_commitments.vk, 638
        Plonk4VerifierWithAccessToDNext.sol, gate_selectors_openings_at_z.proof, 178
        Plonk4VerifierWithAccessToDNext.sol, copy_permutation_polys_openings_at_z.proof, 325
        Plonk4VerifierWithAccessToDNext.sol, state_polys_openings_at_z.proof, 171
        Plonk4VerifierWithAccessToDNext.sol, state_polys_openings_at_z_omega.proof, 175
        Plonk4VerifierWithAccessToDNext.sol, alpha_values.state, 212



## Unnecessary index init


In for loops you initialize the index to start from 0, but it already initialized to 0 in default and this assignment cost gas. 
It is more clear and gas efficient to declare without assigning 0 and will have the same meaning:

### Code instances:

        Plonk4VerifierWithAccessToDNext.sol, 624
        Verifier.sol, 183
        Plonk4VerifierWithAccessToDNext.sol, 301
        Plonk4VerifierWithAccessToDNext.sol, 560
        Verifier.sol, 172
        Getters.sol, 163
        Verifier.sol, 134
        Plonk4VerifierWithAccessToDNext.sol, 148
        Plonk4VerifierWithAccessToDNext.sol, 178
        AllowList.sol, 103
        Verifier.sol, 178
        Plonk4VerifierWithAccessToDNext.sol, 144
        Merkle.sol, 28
        Plonk4VerifierWithAccessToDNext.sol, 175
        Plonk4VerifierWithAccessToDNext.sol, 164
        Plonk4VerifierWithAccessToDNext.sol, 307
        Plonk4VerifierWithAccessToDNext.sol, 473
        Verifier.sol, 188
        PairingsBn254.sol, 241
        Plonk4VerifierWithAccessToDNext.sol, 325
        Verifier.sol, 139
        Plonk4VerifierWithAccessToDNext.sol, 171
        Plonk4VerifierWithAccessToDNext.sol, 181
        AllowList.sol, 69
        Mailbox.sol, 223
        Plonk4VerifierWithAccessToDNext.sol, 449
        Plonk4VerifierWithAccessToDNext.sol, 615
        Verifier.sol, 164



## Storage double reading. Could save SLOAD

Reading a storage variable is gas costly (SLOAD). In cases of multiple read of a storage variable in the same scope, caching the first read (i.e saving as a local variable) can save gas and decrease the
 overall gas uses. The following is a list of functions and the storage variables that you read twice: 

### Code instance:

        L2ETHBridge.sol: CONVENTIONAL_ETH_ADDRESS is read twice in withdraw



## Unnecessary default assignment


Unnecessary default assignments, you can just declare and it will save gas and have the same meaning.
    

### Code instance:

        TranscriptLib.sol (L#11) : uint32 constant DST_0 = 0;



## Unnecessary cast


    
### Code instance:

        uint256 Mailbox.sol.serializeL2Transaction - unnecessary casting uint256(_txId)



## Use unchecked to save gas for certain additive calculations that cannot overflow


You can use unchecked in the following calculations since there is no risk to overflow:

### Code instance:

        DiamondCut.sol (L#52) - bool upgradeNoticePeriodPassed = block.timestamp >= s.diamondCutStorage.proposedDiamondCutTimestamp + UPGRADE_NOTICE_PERIOD; 



## Consider inline the following functions to save gas


    You can inline the following functions instead of writing a specific function to save gas.
    (see https://github.com/code-423n4/2021-11-nested-findings/issues/167 for a similar issue.)

    
### Code instances

        PriorityQueue.sol, getFirstUnprocessedPriorityTx, { return _queue.head; }
        PriorityQueue.sol, getTotalPriorityTxs, { return _queue.tail; }
        PairingsBn254.sol, P1, { return G1Point(1, 2); }
        PairingsBn254.sol, new_g2, { return G2Point(x, y); }



## Inline one time use functions


The following functions are used exactly once. Therefore you can inline them and save gas and improve code clearness.
    

### Code instances:

        L2ContractHelper.sol, bytecodeLen
        Plonk4VerifierWithAccessToDNext.sol, lookup_quotient_contribution
        PairingsBn254.sol, point_sub_into_dest
        ReentrancyGuard.sol, _initializeReentrancyGuard
        Plonk4VerifierWithAccessToDNext.sol, evaluate_vanishing
        PairingsBn254.sol, add_assign
        PairingsBn254.sol, pow
        Plonk4VerifierWithAccessToDNext.sol, aggregated_linearization_commitment
        Plonk4VerifierWithAccessToDNext.sol, evaluate_l0_at_point
        PairingsBn254.sol, mul_assign
        Verifier.sol, deserialize_proof
        Plonk4VerifierWithAccessToDNext.sol, initialize_transcript
        Verifier.sol, get_verification_key
        Plonk4VerifierWithAccessToDNext.sol, lookup_linearization_contribution
        L2ERC20Bridge.sol, _deployL2Token
        L2ETHBridge.sol, _getL1WithdrawMessage
        PairingsBn254.sol, pairing
        L2ERC20Bridge.sol, _getL1WithdrawMessage
        PairingsBn254.sol, sub_assign
        PairingsBn254.sol, new_g1_checked
        Plonk4VerifierWithAccessToDNext.sol, verify_quotient_evaluation
        Plonk4VerifierWithAccessToDNext.sol, final_pairing



## Do not cache msg.sender


We recommend not to cache msg.sender since calling it is 2 gas while reading a variable is more.


### Code instance:

        https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L47

