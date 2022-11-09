### [L-01] ```require()``` should be used instead of ```assert()```


#### Impact
Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as ```require()```/```revert()``` do. ```assert()``` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.


#### Findings:
```
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol:L16        assert(SECURITY_COUNCIL_APPROVALS_FOR_EMERGENCY_UPGRADE > 0);

```

### [L-02] Avoid floating pragmas for non-library contracts.


#### Impact
While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### Findings:
```
2022-10-zksync/zksync/contracts/L2ContractHelper.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/ExternalDecoder.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/L2ERC20Bridge.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/L2ETHBridge.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/interfaces/IL2Bridge.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/interfaces/IL2StandardToken.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/interfaces/IL2EthInitializable.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/zksync/contracts/bridge/interfaces/IL1Bridge.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/common/L2ContractHelper.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/common/interfaces/IAllowList.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/common/AllowListed.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/common/AllowList.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/common/ReentrancyGuard.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IMailbox.sol:L3 pragma solidity ^0.8;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IExecutor.sol:L3 pragma solidity ^0.8;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IGetters.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IGovernance.sol:L3 pragma solidity ^0.8;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IDiamondCut.sol:L3 pragma solidity ^0.8;
2022-10-zksync/ethereum/contracts/zksync/interfaces/IZkSync.sol:L3 pragma solidity ^0.8;
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/Executor.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/Governance.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/Base.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/Getters.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/facets/DiamondCut.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/DiamondProxy.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/Config.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/Storage.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/zksync/DiamondInit.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/bridge/interfaces/IL2Bridge.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/bridge/interfaces/IL1Bridge.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/bridge/L1EthBridge.sol:L3 pragma solidity ^0.8.0;
2022-10-zksync/ethereum/contracts/bridge/L1ERC20Bridge.sol:L3 pragma solidity ^0.8.0;

```

### [L-03] ```require()```/```revert()``` statements should have descriptive strings.


#### Impact
Consider adding descriptive strings in ```require()```/```revert()```.


#### Findings:
```
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol:L96        require(msg.sender == l2Bridge);

2022-10-zksync/zksync/contracts/bridge/L2ETHBridge.sol:L50        require(_l1Token == CONVENTIONAL_ETH_ADDRESS);

```

### [L-04] ```_safemint()``` should be used rather than ```_mint()``` wherever possible


#### Impact
```_mint()``` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of ```_safeMint()``` which ensures that the recipient is either an EOA or implements ```IERC721Receiver```. Both [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/transmissions11/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function


#### Findings:
```
2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol:L103        _mint(_to, _amount);

```

### [L-05] zero-address checks are missing


#### Impact
Zero-address checks are a best practice for input validation of critical address parameters. Accidental use of zero-addresses may result in exceptions, burn fees/tokens, or force redeployment of contracts.

#### Findings:
https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L36
https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L41
https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L31




### [N-01] Open TODOs


#### Impact
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.


#### Findings:
```
2022-10-zksync/ethereum/contracts/zksync/interfaces/IExecutor.sol:L56    /// TODO: The verifier integration is not finished yet, change the structure for compatibility later
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol:L94        // TODO: estimate gas for L1 execute
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol:L127        // TODO: Restore after stable priority op fee modeling. (SMA-1230)
2022-10-zksync/ethereum/contracts/zksync/facets/Mailbox.sol:L169                layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)
2022-10-zksync/ethereum/contracts/zksync/Config.sol:L28 // TODO: change constant to the real root hash of empty Merkle tree (SMA-184)

```


