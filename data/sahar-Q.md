All contracts have a floating version of the compiler, which may cause problems for the entire project due to many contracts and files. It is recommended to lock all versions of compilers of all contracts on a specific version.

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L3

________________________________________________________________________

The following four lines of the program contain require without providing any comment in the  comment field. Although this helps reduce gas consumption, using the predefined error messages in these require commands is recommended.


https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1EthBridge.sol#L145

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1EthBridge.sol#L221

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1EthBridge.sol#L224

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1EthBridge.sol#L238
________________________________________________________________________

Although this issue does not create a risk, it is recommended to change the place of command “emit”   and “require”  in order to observe a logical correct order in the function.

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/DiamondCut.sol#L35

________________________________________________________________________
This line of the program needs to be re-examined and needs more inspection to determine if using  “ &&”  instead of “||”  is more logical in this line or not.

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/DiamondCut.sol#L55


________________________________________________________________________
https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L89

This issue does not create a risk, but it is recommended to use “ _diamondCut”  instead of “diamondCut”  to comply with the naming standards of internal functions. And also, this function needs to be re-examined to determine whether it needs modifiers or not.
________________________________________________________________________
It should be controlled in a modifier or in a separate contract whether a similar and identical address is used for all bridges in all contracts or not. If this is not done, it is possible that different addresses and bridges are used inadvertently or fraudulently, and unexpected results may be obtained.

 example:
https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2StandardERC20.sol#L46


https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ETHBridge.sol#L31
________________________________________________________________________
The hash calculation argument for the transaction must be “msg.value or amount”, not “zkSyncFee”. This wrong argument makes the whole Tx hash calculation wrong  

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1EthBridge.sol#L98
  

For example, the below line has correct argument: 

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L120

 ________________________________________________________________________
Using the "_executedBlockIdx" variable in these calculations has the wrong logic and may cause confusion in the order of execution of blocks. It is suggested that by removing this variable in this command, the blocks are executed one by one and sequentially

https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L192

