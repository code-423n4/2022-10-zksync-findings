### Gas Optimizations List
| Number | Optimization Details | Context |
|:--:|:-------| :-----:|
| [G-01] |No need ```return``` keyword for gas efficient | 2 |
| [G-02] | Functions guaranteed to revert_ when callled by normal users can be marked `payable` | 5 |
| [G-03] | ```x -= y (x += y) ``` costs more gas than ```x = x – y (x = x + y)``` for state variables | 2 |
| [G-04] | Optimize names to save gas | All contracts |
| [G-05] | Use assembly to check for ```address(0)``` | 9 |
| [G-06] | Use ``assembly`` to write _address storage values_ | 9 |
| [G-07] | The solady Library's Ownable contract is significantly gas-optimized, which can be used |  |
| [G-08] | Setting the _constructor_ to `payable` | 9 |
| [G-09] | Using double require instead of && consumes less gas | 3 |
| [G-10] | Free gas savings for using solidity 0.8.10+ | All contracts |

Total 10 issues

### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |Use `v4.8.0 OpenZeppelin` contracts |
| [S-02] |Missing `zero-address` check in `constructor |

Total 2 suggestions



### [G-01] No need ```return``` keyword for gas efficient

**Context:**

```solidity
ethereum/contracts/zksync/facets/DiamondCut.sol:
  124      /// @dev Set up the proposed diamond cut state to the default values
  125:     /// @return Whether the proposal is reset or it was already empty
  126:     function _resetProposal() private returns (bool) {
  127:         if (s.diamondCutStorage.proposedDiamondCutTimestamp == 0) {
  128:             return false;
  129:         }
  130: 
  131:         delete s.diamondCutStorage.proposedDiamondCutHash;
  132:         delete s.diamondCutStorage.proposedDiamondCutTimestamp;
  133:         delete s.diamondCutStorage.securityCouncilEmergencyApprovals;
  134: 
  135:         return true;
  136:     }
```

**Recommendation:**
You should remove the ```return``` keyword from the specified contexts.


### [G-02]  Functions guaranteed to revert_ when callled by normal users can be marked `payable`


**Context:** 

```solidity
5 results - 1 file

ethereum/contracts/common/AllowList.sol:

   56      /// @param _enable Whether enable or disable the public access
   57:     function setPublicAccess(address _target, bool _enable) external onlyOwner {


   64      /// @param _enables The array of boolean flags, whether enable or disable the public access to the corresponding target address
   65:     function setBatchPublicAccess(address[] calldata _targets, bool[] calldata _enables) external onlyOwner {

   93          bool[] calldata _enables
   94:     ) external onlyOwner {
   95          uint256 callersLength = _callers.length;

  117          bool _enable
  118:     ) external onlyOwner {
  119          _setPermissionToCall(_caller, _target, _functionSig, _enable);

  139      /// @param _newPendingOwner Address of the new owner
  140:     function setPendingOwner(address _newPendingOwner) external onlyOwner {
```

**Description:**
If a function modifier or require such as onlyOwner-admin is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

**Recommendation:**
Functions guaranteed to revert when called by normal users can be marked payable  (for only ```onlyOwner or admin``` functions)

**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs.

```js
contract GasTest is DSTest {
    Contract0 c0;
    Contract1 c1;
    
    function setUp() public {
        c0 = new Contract0();
        c1 = new Contract1();
    }
    
    function testGas() public {
        c0.foo();
        c1.foo();
    }
}

contract Contract0 {
    uint256inverse;
    
    function foo() external {
       inverse++;
    }
}

contract Contract1 {
    uint256inverse;
    
    function foo() external payable {
       inverse++;
    }
}
```
**Gas Report:**
```js
╭───────────────────────────────────────────┬─────────────────┬───────┬────────┬───────┬─────────╮
│ src/test/GasTest.t.sol:Contract0 contract ┆                 ┆       ┆        ┆       ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 44293                                     ┆ 252             ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ foo                                       ┆ 22308           ┆ 22308 ┆ 22308  ┆ 22308 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴───────┴────────┴───────┴─────────╯
╭───────────────────────────────────────────┬─────────────────┬───────┬────────┬───────┬─────────╮
│ src/test/GasTest.t.sol:Contract1 contract ┆                 ┆       ┆        ┆       ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 41893                                     ┆ 240             ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ foo                                       ┆ 22284           ┆ 22284 ┆ 22284  ┆ 22284 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴───────┴────────┴───────┴─────────╯
```


### [G-03] ```x -= y (x += y) ``` costs more gas than ```x = x – y (x = x + y)``` for state variables

**Description:**
```x -= y``` costs more gas than ```x = x – y``` for state variables.

**Context:**

```solidity
2 results - 2 files

ethereum/contracts/zksync/facets/DiamondCut.sol:
  29:         s.diamondCutStorage.currentProposalId += 1;

ethereum/contracts/zksync/libraries/TranscriptLib.sol:
  44:         self.challenge_counter += 1;
```

**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs.

```js
contract GasTest is DSTest {
  
    Contract0 c0;
    Contract1 c1;
    
    function setUp() public {
        c0 = new Contract0();
        c1 = new Contract1();
    }
    
    function testGas() public {
        c0.swap(2,3);
        c1.swap1(2,3);
    }
}
contract Contract0 {
    uint256 public amountIn = 10;
    
    
    function swap(uint256 amountInToBin, uint256 fee)  external returns (uint256 ) {
       
       return amountIn -= amountInToBin + fee;
    }
}

contract Contract1 {
    uint256 public amountIn = 10;
    
    function swap1(uint256 amountInToBin, uint256 fee)  external returns (uint256 result1) {
       return (amountIn = amountIn - (amountInToBin + fee));
    }
}
```
**Gas Report:**
```js
╭──────────────────────────────────────┬─────────────────┬──────┬────────┬──────┬─────────╮
│ src/test/test.sol:Contract0 contract ┆                 ┆      ┆        ┆      ┆         │
╞══════════════════════════════════════╪═════════════════╪══════╪════════╪══════╪═════════╡
│ Deployment Cost                      ┆ Deployment Size ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 83017                                ┆ 341             ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                        ┆ min             ┆ avg  ┆ median ┆ max  ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ swap                                 ┆ 5454            ┆ 5454 ┆ 5454   ┆ 5454 ┆ 1       │
╰──────────────────────────────────────┴─────────────────┴──────┴────────┴──────┴─────────╯
╭──────────────────────────────────────┬─────────────────┬──────┬────────┬──────┬─────────╮
│ src/test/test.sol:Contract1 contract ┆                 ┆      ┆        ┆      ┆         │
╞══════════════════════════════════════╪═════════════════╪══════╪════════╪══════╪═════════╡
│ Deployment Cost                      ┆ Deployment Size ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 83017                                ┆ 341             ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                        ┆ min             ┆ avg  ┆ median ┆ max  ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ swap1                                ┆ 5431            ┆ 5431 ┆ 5431   ┆ 5431 ┆ 1       │
╰──────────────────────────────────────┴─────────────────┴──────┴────────┴──────┴─────────╯
```

### [G-04] Optimize names to save gas

**Context:** 
All Contracts

**Description:** 
Contracts most called functions could simply save gas by function ordering via ```Method ID```. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because ```22 gas``` are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

**Recommendation:** 
Find a lower ```method ID``` name for the most called functions for example Call() vs. Call1() is cheaper by ```22 gas```
For example, the function IDs in the ```Executer.sol ``` contract will be the most used; A lower method ID may be given.

**Proof of Consept:**
https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

Executer.sol function names can be named and sorted according to METHOD ID

```js
Sighash   |   Function Signature
========================
fabfe914  =>  _commitOneBlock(StoredBlockInfo,CommitBlockInfo)
09b2eb4a  =>  _calculateBlockHash(StoredBlockInfo,CommitBlockInfo)
986b4aa3  =>  _processL2Logs(CommitBlockInfo)
e5a99663  =>  commitBlocks(StoredBlockInfo,CommitBlockInfo[])
430bf418  =>  _collectOperationsFromPriorityQueue(uint256)
b82ece5b  =>  _executeOneBlock(StoredBlockInfo,uint256)
7bf63b26  =>  executeBlocks(StoredBlockInfo[])
220b42df  =>  proveBlocks(StoredBlockInfo,StoredBlockInfo[],ProofInput)
af17aec7  =>  _getBlockProofPublicInput(bytes32,bytes32,ProofInput,VerifierParams)
91d21b00  =>  _verifyRecursivePartOfProof(uint256[])
a9a2d18a  =>  revertBlocks(uint256)
cc5c2258  =>  _maxU256(uint256,uint256)
61da23b6  =>  _createBlockCommitment(CommitBlockInfo)
782cce1d  =>  _blockPassThroughData(CommitBlockInfo)
cef9c155  =>  _blockMetaParameters()
c8fc5069  =>  _blockAuxilaryOutput(CommitBlockInfo)
bbea6aec  =>  _hashStoredBlockInfo(StoredBlockInfo)
```

### [G-05] Use assembly to check for ```address(0)```

**Context:** 
```solidity
9 results - 6 files

ethereum/contracts/common/AllowList.sol:
  32      constructor(address _owner) {
  33:         require(_owner != address(0), "kq");

ethereum/contracts/zksync/DiamondProxy.sol:
  29:         require(facetAddress != address(0), "F"); // Proxy has no facet for this selector

ethereum/contracts/zksync/facets/Getters.sol:
  148:         require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");

ethereum/contracts/zksync/libraries/Diamond.sol:
  125  
  126:         require(_facet != address(0), "G"); // facet with zero address cannot be added
  127  

  149  
  150:         require(_facet != address(0), "K"); // cannot replace facet with zero address
  151  

  155              SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
  156:             require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address
  157  

  175              SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
  176:             require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet
  177  

zksync/contracts/bridge/L2ERC20Bridge.sol:
  94          address l1Token = l1TokenAddress[_l2Token];
  95:         require(l1Token != address(0), "yh");
  96  

zksync/contracts/bridge/L2StandardERC20.sol:
  44          require(l1Address == address(0), "in5"); // Is already initialized
  45:         require(_l1Address != address(0), "in6"); // Should be non-zero address
```

**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs.

```js
contract GasTest is DSTest {
    Contract0 c0;
    Contract1 c1;
    function setUp() public {
        c0 = new Contract0();
        c1 = new Contract1();
    }
    function testGas() public view {
        c0.setOperator(address(this));
        c1.setOperator(address(this));
    }
}
contract Contract0 {
    function setOperator(address operator_) public pure {
        require(operator_) != address(0), "INVALID_RECIPIENT");    }
}
contract Contract1 {
    function setOperator(address operator_) public pure {
        assembly {
            if iszero(operator_) {
                mstore(0x00, "Callback_InvalidParams")
                revert(0x00, 0x20)
            }
        }
    }
}
```
**Gas Report:**
```js
╭───────────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/GasTest.t.sol:Contract0 contract ┆                 ┆     ┆        ┆     ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 50899                                     ┆ 285             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg ┆ median ┆ max ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ setOperator                               ┆ 258             ┆ 258 ┆ 258    ┆ 258 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴─────┴────────┴─────┴─────────╯
╭───────────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/GasTest.t.sol:Contract1 contract ┆                 ┆     ┆        ┆     ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 44893                                     ┆ 255             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg ┆ median ┆ max ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ setOperator                               ┆ 252             ┆ 252 ┆ 252    ┆ 252 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴─────┴────────┴─────┴─────────╯
```

### [G-06] Use ``assembly`` to write _address storage values_

**Context:**

```solidity
9 results - 9 files

ethereum/contracts/bridge/L1ERC20Bridge.sol:
  56      /// @dev Initialize the implementation to prevent Parity hack.
  57:     constructor(IMailbox _mailbox, IAllowList _allowList) reentrancyGuardInitializer {
  58          zkSyncMailbox = _mailbox;

ethereum/contracts/bridge/L1EthBridge.sol:
  47      /// @dev Initialize the implementation to prevent Parity hack.
  48:     constructor(IMailbox _mailbox, IAllowList _allowList) reentrancyGuardInitializer {
  49          zkSyncMailbox = _mailbox;

ethereum/contracts/common/AllowList.sol:
  31  
  32:     constructor(address _owner) {
  33          require(_owner != address(0), "kq");

ethereum/contracts/zksync/DiamondInit.sol:
  14  contract DiamondInit is Base {
  15:     constructor() reentrancyGuardInitializer {}
  16  

ethereum/contracts/zksync/DiamondProxy.sol:
   9  contract DiamondProxy {
  10:     constructor(uint256 _chainId, Diamond.DiamondCutData memory _diamondCut) {
  11          // Check that the contract is deployed on the expected chain.

ethereum/contracts/zksync/facets/DiamondCut.sol:
  12  contract DiamondCutFacet is Base, IDiamondCut {
  13:     constructor() {
  14          // Caution check for config value.


zksync/contracts/bridge/L2ERC20Bridge.sol:
  30  
  31:     constructor(
  32          address _l1Bridge,

zksync/contracts/bridge/L2ETHBridge.sol:
  29  
  30:     constructor(address _l1Bridge) {
  31          l1Bridge = _l1Bridge;

zksync/contracts/bridge/L2StandardERC20.sol:
  38      /// @dev Initialize the implementation to prevent Parity hack.
  39:     constructor() initializer {}
  40 
```

**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs.

```js
contract GasTestFoundry is DSTest {
    Contract1 c1;
    Contract2 c2;
    
    function setUp() public {
        c1 = new Contract1();
        c2 = new Contract2();
    }
    
    function testGas() public {
        c1.setRewardTokenAndAmount(0x5B38Da6a701c568545dCfcB03FcB875f56beddC4,356);
        c2.setRewardTokenAndAmount(0x5B38Da6a701c568545dCfcB03FcB875f56beddC4,356);
    }
}

contract Contract1  {
    address rewardToken ;
    uint256 reward;

    function setRewardTokenAndAmount(address token_, uint256 reward_) external {
        rewardToken = token_;
        reward = reward_;
    }
}

contract Contract2  {
    address rewardToken ;
    uint256 reward;

    function setRewardTokenAndAmount(address token_, uint256 reward_) external {
        assembly {
            sstore(rewardToken.slot, token_)
            sstore(reward.slot, reward_)           

        }
    }
}
```
**Gas Report:**
```js
╭───────────────────────────────────────────┬─────────────────┬───────┬────────┬───────┬─────────╮
│ src/test/GasTest.t.sol:Contract1 contract ┆                 ┆       ┆        ┆       ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 50899                                     ┆ 285             ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ setRewardTokenAndAmount                   ┆ 44490           ┆ 44490 ┆ 44490  ┆ 44490 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴───────┴────────┴───────┴─────────╯
╭───────────────────────────────────────────┬─────────────────┬───────┬────────┬───────┬─────────╮
│ src/test/GasTest.t.sol:Contract2 contract ┆                 ┆       ┆        ┆       ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 38087                                     ┆ 221             ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ setRewardTokenAndAmount                   ┆ 44457           ┆ 44457 ┆ 44457  ┆ 44457 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴───────┴────────┴───────┴─────────╯
```

### [G-07] The solady Library's Ownable contract is significantly gas-optimized, which can be used

**Description:**
The project uses the `onlyOwner` authorization model I recommend using _Solady's highly gas optimized contract._

https://github.com/Vectorized/solady/blob/main/src/auth/OwnableRoles.sol


### [G-08] Setting the _constructor_ to `payable`

**Description:**
You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of ```msg.value == 0``` and saves ```13 gas``` on deployment with no security risks.

**Context:**
```solidity
9 results - 9 files

ethereum/contracts/bridge/L1ERC20Bridge.sol:
  56      /// @dev Initialize the implementation to prevent Parity hack.
  57:     constructor(IMailbox _mailbox, IAllowList _allowList) reentrancyGuardInitializer {
  58          zkSyncMailbox = _mailbox;

ethereum/contracts/bridge/L1EthBridge.sol:
  47      /// @dev Initialize the implementation to prevent Parity hack.
  48:     constructor(IMailbox _mailbox, IAllowList _allowList) reentrancyGuardInitializer {
  49          zkSyncMailbox = _mailbox;

ethereum/contracts/common/AllowList.sol:
  31  
  32:     constructor(address _owner) {
  33          require(_owner != address(0), "kq");


ethereum/contracts/zksync/DiamondInit.sol:
  14  contract DiamondInit is Base {
  15:     constructor() reentrancyGuardInitializer {}
  16  

ethereum/contracts/zksync/DiamondProxy.sol:
   9  contract DiamondProxy {
  10:     constructor(uint256 _chainId, Diamond.DiamondCutData memory _diamondCut) {
  11          // Check that the contract is deployed on the expected chain.

ethereum/contracts/zksync/facets/DiamondCut.sol:
  12  contract DiamondCutFacet is Base, IDiamondCut {
  13:     constructor() {
  14          // Caution check for config value.

zksync/contracts/bridge/L2ERC20Bridge.sol:
  30  
  31:     constructor(
  32          address _l1Bridge,

zksync/contracts/bridge/L2ETHBridge.sol:
  29  
  30:     constructor(address _l1Bridge) {
  31          l1Bridge = _l1Bridge;

zksync/contracts/bridge/L2StandardERC20.sol:
  38      /// @dev Initialize the implementation to prevent Parity hack.
  39:     constructor() initializer {}
  40 
```

**Recommendation:**
Set the constructor to ```payable```

**Proof Of Concept:**
https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/5?u=pcaversaccio

The optimizer was turned on and set to 10000 runs

```js
contract GasTestFoundry is DSTest {
    
    Contract1 c1;
    Contract2 c2;
    
    function setUp() public {
        c1 = new Contract1();
        c2 = new Contract2();
    }
    
    function testGas() public {
        c1.x();
        c2.x();
    }
}

contract Contract1 {
    
    uint256 public dummy;
    constructor() payable {
        dummy = 1;
    }
    
    function x() public {

    }
}

contract Contract2 {
    
    uint256 public dummy;
    constructor() {
        dummy = 1;
    }
    
    function x() public {
    }
}
```
**Gas Report:**
```js
╭───────────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/GasTest.t.sol:Contract1 contract ┆                 ┆     ┆        ┆     ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 49563                                     ┆ 159             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤

╭───────────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/GasTest.t.sol:Contract2 contract ┆                 ┆     ┆        ┆     ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 49587                                     ┆ 172             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
```

### [G-09] Using double require instead of && consumes less gas

**Context:**

```solidity
3 results - 2 files

ethereum/contracts/common/AllowList.sol:
  96          require(
  97:             callersLength == _targets.length &&
  98:                 callersLength == _functionSigs.length &&
  99                  callersLength == _enables.length,

ethereum/contracts/common/L2ContractHelper.sol:
  65:         require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
```

###  [G-10] Free gas savings for using solidity 0.8.10+

**Context:**
All Contracts

**Description:**
Solidity 0.8.10 has a useful change which reduced gas costs of external calls which expect a return value: https://blog.soliditylang.org/2021/11/09/solidity-0.8.10-release-announcement/
Code Generator: Skip existence check for external contract if return data is expected. In this case, the ABI decoder will revert if the contract does not exist.
**Recommendation:**
Consider to upgrade pragma to at least 0.8.10

###  [S-01] Missing `zero-address` check in `constructor`

**Description:**
Missing checks for zero-addresses may lead to infunctional protocol, if the variable addresses are updated incorrectly. It also wast gas as it requires the redeployment of the contract.

```solidity
2 results - 2 files

zksync/contracts/bridge/L2ERC20Bridge.sol:
   30  
   31:     constructor(
   32          address _l1Bridge,


zksync/contracts/bridge/L2ETHBridge.sol:
  29  
  30:     constructor(address _l1Bridge) {
  31          l1Bridge = _l1Bridge;
```

### [S-02] Use `v4.8.0 OpenZeppelin` contracts

**Description:**
The upcoming v4.8.0 version of OpenZeppelin provides many small gas optimizations.

https://github.com/OpenZeppelin/openzeppelin-contracts/releases/tag/v4.8.0-rc.0

```js
v4.8.0-rc.0
⛽ Many small optimizations
```