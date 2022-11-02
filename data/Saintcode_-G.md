## USE CALLDATA INSTEAD OF MEMORY

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution.

When arguments are read-only on external functions, the data location should be `calldata`

-L1ERC20Bridge.sol line 260
-L1EthBridge.sol line 214
-L2ContractHelper.sol lines 43, 47
-Executor lines 152, 190
-Mailbox lines 43, 53 
-Diamond.sol lines 89, 119


## <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP

Reading array length at each iteration of the loop consumes more gas than necessary.
In the best case scenario (length read on a memory variable), caching the array length in the stack saves around 3 gas per iteration. In the worst case scenario (external calls at each iteration), the amount of gas wasted can be massive.

Consider storing the array’s length in a variable before the for-loop, and use this new variable instead

Instances: 
-Executor.sol line 111 

## <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES

-DiamondCut.sol line 29


## USE MULTIPLE `REQUIRE()` STATMENTS INSTEAD OF `REQUIRE(EXPRESSION && EXPRESSION && ...)`

-AllowList.sol line 96
-L2ContractHelper.sol line 65

## `>=` COSTS LESS GAS THAN `>`

The compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, which saves 3 gas

Instances:
-L1ERC20Bridge.sol lines 117, 210
-DiamondCut.sol line 16
-Diamond.sol line 100
-Merkle.sol line 23

## `++I/I++` SHOULD BE `UNCHECKED{++I}`/`UNCHECKED{I++}` WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop
`
   for (uint256 i = 0; i < orders.length; /** NOTE: Removed i++ **/ ) {
           // Do the thing
           // Unchecked pre-increment is cheapest
           unchecked { ++i; }   
}  `

Instances:
-Diamond.sol lines 94, 132, 153, 173

## USING `PRIVATE` RATHER THAN `PUBLIC` FOR CONSTANTS, SAVES GAS

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table


## USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE GAS
Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas



