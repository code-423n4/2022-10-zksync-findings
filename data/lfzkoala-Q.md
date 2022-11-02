This is the QA report for the zkSync contest. This report includes all low/non-critical issues found. Issue is described in each subject. 

# Subject 1: 
l2Bridge should be validated before usage. 

### description: 
Before the initialization, the L2 bridge should be a zero address which means the bridge is initialized. 

### recommendation: 
Check l2Bridge is equal to zero address by using ``require(l2Bridge == address(0))`` before using it. 

### locations: 
contracts/bridge/L1EthBridge.sol#48

# Subject 2: 
Redundant Input Type

### description: 
The function ``l2TokenAddress`` doesn't need any input because it just returns zero address, so you can remove the input ``address`` type. 

### recommendation: 
remove the ``address`` type in the input. 

### locations: 
contracts/bridge/L1ERC20Bridge.sol#206

# Subject 3: 
Undefined Functions

### description: 
The ``l1TokenAddress`` and ``l1Bridge`` functions are undefined but their interfaces are defined. 

### recommendation: 
Make sure these interfaces are needed. If these methods are needed, add the corresponding function implementations. 

### locations: 
contracts/bridge/interfaces/IL2Bridge.sol#21,25

# Subject 4: 
Distinguish Return Value. 

### description: 
The force deployment of L2 contract and the general initialization both return the same constant value ``DIAMOND_INIT_SUCCESS_RETURN_VALUE``. It's better to distinguish these two different initialization. 

### recommendation: 
Use a different constant return value for force deployment. 

### locations: 
contracts/zksync/DiamondUpgradeInit.sol#27

# Subject 5: 
Moving the group 2 fixed generator to a common place. 

### description: 
The pairing group2 generator is a fixed value and being used in multiple places. It's better to move it to a common place for usage. 

### recommendation: 
Move the group2 generator to a common place for use. 

### locations: 
contracts/zksync/facets/Executor.sol#334-341


# Subject 6: 
Confusing Variable Name

### description: 
The variable name _newLastBlock is confusing, because it means a block number after which blocks should be reverted. Suggest changing it to _newLastBlockNumber or something similar. 

### recommendation: 
Change ``_newLastBlock`` to another name, for example, ``_newLastBlockNumber`` or something similar. 

### locations: 
contracts/zksync/facets/Executor.sol#362

# Subject 7: 
Unused Input

### description: 
The function ``_blockMetaParameters`` doesn't use the input calldata ``_block``. 

### recommendation: 
Remove the input calldata ``_block`` and modify the function. 

### locations: 
contracts/zksync/facets/Executor.sol#398

# Subject 8: 
Confusing Name And Data Structure Usage

### description: 
Naming ``PriorityQueue`` is confusing here because the implementation is just a regular queue with FIFO rules. PriorityQueue is usually realized by some data structure like max heap or min heap. 

### recommendation: 
Consider changing the name or determine which kind of data structure you will use in the future. 

### locations: 
contracts/zksync/libraries/PriorityQueue.sol

# Subject 9: 
Unused Variables

### description: 
``totalSupply`` and ``balanceOf`` these two variables are never used. Especially missing ``totalSupply`` could be critical because we need to set what's the total amount of tokens and restrict it. 

### recommendation: 
Add corresponding code section which uses these variables. 

### locations: 
zksync/contracts/bridge/L2ETHBridge.sol#16,19


# Subject 10: 
Unused Constants

### description: 
``BOOTLOADER_ADDRESS`` and ``VALUE_SIMULATOR_SYSTEM_CONTRACT_ADDRESS`` these two constants are never used. 

### recommendation: 
Add corresponding code section which uses these constants. 

### locations: 
zksync/contracts/bridge/L2ContractHelper.sol#19-20


