## [G-01] Optimizations with assembly
### [G-01.1] Use assembly for math (add, sub, mul, div)
### [G-01.2] Use assembly to check for address(0)
File Diamond.Proxy.sol [29](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L29)

File Diamond.sol: [126](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L126), [135](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L135), [150](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L150), [170](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L170), [278](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/libraries/Diamond.sol#L278)

File AllowList.sol: [33](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#L33)

### [G-01.3] Use assembly to store storage values
File DiamondCut.sol: [27-29](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L27-L29), [68](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L68), [83-85](https://github.com/code-423n4/2022-10-zksync/blob/xmain/ethereum/contracts/zksync/facets/DiamondCut.sol#L83-L85), [98](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L98), [109](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L109), [114](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L114)

File Executor.sol [164](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L164), [173](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L173), [202](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L202), [215](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L215), [270](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L270), [341](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L341), [343](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L343) 

## [G-02] X += Y or X -= Y costs more gas than  X = X + Y or X = X - Y  for state variables
File DiamondCut.sol: [29](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L29)

## [G-03] Cache storage values in memory to minimize SLOADs
`s.totalBlocksVerified` should be cached 
File Executor.sol [340](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L340), [345](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L345)

```
        if (newTotalBlocksCommitted < s.totalBlocksVerified) { // 1 SLOAD
            s.totalBlocksVerified = newTotalBlocksCommitted;
        }
        s.totalBlocksCommitted = newTotalBlocksCommitted; // 2 SLOAD

        emit BlocksRevert(s.totalBlocksCommitted, s.totalBlocksVerified, s.totalBlocksExecuted); // 3 SLOAD
```

cache `s.totalBlocksCommitted` to minimize usage in event 
File Executor.sol [345](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L345)
from 
`emit BlocksRevert(s.totalBlocksCommitted, s.totalBlocksVerified, s.totalBlocksExecuted);`
to 
`emit BlocksRevert(newTotalBlocksCommitted, s.totalBlocksVerified, s.totalBlocksExecuted);`

cache `s.totalBlocksExecuted` to minimize usage in event 
File Executor.sol [338](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L338), [345](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L345)


## [G-04] Using calldata instead of memory for read-only arguments in external functions saves gas
File Mailbox.sol: [43](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L43), [53](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L53)

File L2ContractHelper.sol: [43](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L43), [47](https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol#L47)

File ExternalDecoder.so: [10](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/ExternalDecoder.sol#L10), [15](https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/ExternalDecoder.sol#L15)


## [G-05] Optimize names to save gas
public/external function names and public member variable names can be optimized to save gas. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

Files: IDiamondCut, IExecutor.sol, IGetters.sol, IGovernance.sol, IMailbox.sol, IZkSync.sol
