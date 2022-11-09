## Use Storage Instead of Memory for Structs/Arrays
A storage pointer is cheaper since copying a state struct in memory would incur as many SLOADs and MSTOREs as there are slots. In another words, this causes all fields of the struct/array to be read from storage, incurring a Gcoldsload (2100 gas) for each field of the struct/array, and then further incurring an additional MLOAD rather than a cheap stack read. As such, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables will be much cheaper, involving only Gcoldsload for all associated field reads. Read the whole struct/array into a memory variable only when it is being returned by the function, passed into a function that requires memory, or if the array/struct is being read from another memory array/struct. Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L26
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L90
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#L43

## calldata and memory
When running a function we could pass the function parameters as calldata or memory for variables such as strings, bytes, structs, arrays etc. If we are not modifying the passed parameter, we should pass it as calldata because calldata is more gas efficient than memory.

Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory. However, it alleviates the compiler from the `abi.decode()` step that copies each index of the calldata to the memory index, bypassing the cost of 60 gas on each iteration. Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L89
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L121
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L80

## Unchecked SafeMath Saves Gas
"Checked" math, which is default in ^0.8.0 is not free. The compiler will add some overflow checks, somehow similar to those implemented by `SafeMath`. While it is reasonable to expect these checks to be less expensive than the current `SafeMath`, one should keep in mind that these checks will increase the cost of "basic math operation" that were not previously covered. This particularly concerns variable increments in for loops. When no arithmetic overflow/underflow is going to happen, `unchecked { ++i ;}` to use the previous wrapping behavior further saves gas just as in the for loop below as an example:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L173-L179

```
        for (uint256 i = 0; i < selectorsLength;) {
            bytes4 selector = _selectors[i];
            SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
            require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet

            _removeOneFunction(oldFacet.facetAddress, selector);

            unchecked {
                ++i;
            }
        }
```
## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =). As an example, consider replacing >= with the strict counterpart > in the following line of code:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L49-L50

```
        bool approvedBySecurityCouncil = s.diamondCutStorage.securityCouncilEmergencyApprovals >
            SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE - 1;
```
Similarly, as an example, consider replacing <= with the strict counterpart < in the following line of code:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L268

```
        require(currentTotalBlocksVerified < s.totalBlocksCommitted + 1, "q");
```
## `abi.encode()` Costs More Gas Than `abi.encodePacked()`
Changing `abi.encode()` to `abi.encodePacked()` can save gas considering the former pads extra null bytes at the end of the call data, which is unnecessary. Please visit the following the link delineating how `abi.encodePacked()` is more gas efficient in general:

https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison

Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L27
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L61
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L85
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L182

## Payable Access Control Functions Costs Less Gas
Consider marking functions with access control as `payable`. This will save 20 gas on each call by their respective permissible callers for not needing to have the compiler check for `msg.value`. Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L46
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L78
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L225

## Function Order Affects Gas Consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the Optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
  solidity: {
    version: "0.8.0",
    settings: {
      optimizer: {
        enabled: true,
        runs: 1000,
      },
    },
  },
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:

for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI

Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## += and -= Costs More Gas
`+=` generally costs 22 more gas than writing out the assigned equation explicitly. The amount of gas wasted can be quite sizable when repeatedly operated in a loop. As an example, the following line of code could be rewritten as:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L29

```
        s.diamondCutStorage.currentProposalId =  s.diamondCutStorage.currentProposalId + 1;
```
The following line of code should logically be refactored as:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Merkle.sol#L34

```
            _index = _index / 2;
```

## `uint256` Can be Cheaper Than `uint8` and Other Unsigned Integer Type of Smaller Bit Size
When dealing with function arguments or memory values, there is no inherent benefit because the compiler does not pack these values. Your contract’s gas usage may be higher because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. The EVM needs to properly enforce the limits of this smaller type.

It is only more efficient when you can pack variables of uint8, uint16, uint32, uint64, ... into the same storage slot with other neighboring variables smaller than 32 bytes. Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L367
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#L30

## Use of Named Returns for Local Variables Saves Gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable. There are numerous instances entailed throughout all codebases:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L83
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L296
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L349

As an example, the following code block can be refactored as:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L279

```
    function _getBlockProofPublicInput(
        bytes32 _prevBlockCommitment,
        bytes32 _currentBlockCommitment,
        ProofInput calldata _proof,
        VerifierParams memory _verifierParams
    ) internal pure returns (uint256 zkProof) {
        zkProof =
            uint256(
                keccak256(
                    abi.encodePacked(
                        _prevBlockCommitment,
                        _currentBlockCommitment,
                        _verifierParams.recursionNodeLevelVkHash,
                        _verifierParams.recursionLeafLevelVkHash,
                        _verifierParams.recursionCircuitsSetVksHash,
                        _proof.recurisiveAggregationInput
                    )
                )
            ) & INPUT_MASK;
    }
```
## Ternary Over `if ... else`
Using ternary operator instead of the if else statement saves gas. For instance the following code block may be rewritten as:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L278-L289

```
    _init == address(0)
        ? {
            require(_calldata.length == 0, "H"); // Non-empty calldata for zero address
        }
        : {
            // Do not check whether `_init` is a contract since later we check that it returns data.
            (bool success, bytes memory data) = _init.delegatecall(_calldata);
            require(success, "I"); // delegatecall failed

            // Check that called contract returns magic value to make sure that contract logic
            // supposed to be used as diamond cut initializer.
            require(data.length == 32, "lp");
            require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");
        }
```
## Internal Function Only Called Once May Be Embedded to Save Gas
In `L1ERC20Bridge.sol`, `_depositFunds()` may be embedded in `deposit()` both reduce contract size and incur less gas on related function calls:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L116
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L136-L146

Similarly, `_getERC20Getters()` may be embedded inline with `_getDepositL2Calldata()`:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L155
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L164

## Split Require Statements Using &&
Instead of using the `&&` operator in a single require statement to check multiple conditions, using multiple require statements with 1 condition per require statement will save 3 GAS per `&&`. Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L97-L101
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L65

## Private/Internal Function Embedded Modifier Reduces Contract Size
Consider having the logic of a modifier embedded through a private (doesn't matter whether or not the contract entails any child contracts since the private visibility saves even more gas on function calls than the internal visibility) function to reduce contract size if need be. For instance, the following instance of modifier may be rewritten as follows:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/ReentrancyGuard.sol#L38-L41

```
    function _reentrancyGuardInitializer() private view {
        _initializeReentrancyGuard();
    }

    modifier reentrancyGuardInitializer() {
        _reentrancyGuardInitializer();
        _;
    }
```
## `||` Costs Less Gas Than Its Equivalent `&&`
Rule of thumb: `(x && y)` is `(!(!x || !y))`

Even with the 10k Optimizer enabled: `||`, OR conditions cost less than their equivalent `&&`, AND conditions.

As an example, the following code line may be rewritten as:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L65

```
        require(!(version != 1 || _bytecodeHash[1] != bytes1(0)), "zf");
```
