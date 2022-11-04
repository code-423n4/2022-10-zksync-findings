# Qa Report

## ZKSYNC

### `ToDo:`s should be cleaned

Code architecture, incentives and error handling questions should be resolved before deployment

[IExecutor.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IExecutor.sol):

```solidity
line#L56:  /// TODO: The verifier integration is not finished yet, change the structure for compatibility later
```

### Use higher solidity version

Optimizations, bug fixes and additional features come with each solidity `patch` version update

### Use `external` visibility modifier for function that are not being invoked by the contract

[L2ETHBridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol):

```solidity
line#L79:  function l2TokenAddress(address) public pure returns (address) {

line#L84:  function l1TokenAddress(address) public pure override returns (address) {
```

[L2StandardERC20.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol):

```solidity
line#L126: function decimals() public view override returns (uint8) {
```

[Mailbox.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol):

```solidity
line#L89:  function l2TransactionBaseCost(
```

[L1EthBridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol):

```solidity
line#L243: function l2TokenAddress(address) public pure returns (address) {
```

[L1ERC20Bridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol):

```solidity
line#L274: require(bytes4(functionSignature) == this.finalizeWithdrawal.selector, "nt");

line#L282: function l2TokenAddress(address _l1Token) public view returns (address) {
```

### Events should use all three `indexed` keywords for their parameters

[Diamond.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol):

```solidity
line#L16:  event DiamondCut(FacetCut[] facetCuts, address initAddress, bytes initCalldata);
```

[L2StandardERC20.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol):

```solidity
line#L12:  event BridgeInitialization(address indexed l1Token, string name, string symbol, uint8 decimals);
```

[IExecutor.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IExecutor.sol):

```solidity
line#L85:  event BlocksRevert(uint256 totalBlocksCommitted, uint256 totalBlocksVerified, uint256 totalBlocksExecuted);
```

### Contracts should use a fixed compiler version to avoid potential bugs

[DiamondCut.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol):

```solidity
line#L3:   pragma solidity ^0.8.0;
```

[Mailbox.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol):

```solidity
line#L3:   pragma solidity ^0.8.0;
```

[Base.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Base.sol):

```solidity
line#L3:   pragma solidity ^0.8.0;
```

[Governance.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol):

```solidity
line#L3:   pragma solidity ^0.8.0;
```

[Config.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Config.sol):

```solidity
line#L3:   pragma solidity ^0.8.0;
```

[DiamondProxy.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondProxy.sol):

```solidity
line#L3:   pragma solidity ^0.8.0;
```

[L2ERC20Bridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol):

```solidity
line#L3:   pragma solidity ^0.8.0;
```

[L1EthBridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol):

```solidity
line#L3:   pragma solidity ^0.8.0;
```

[L2ETHBridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol):

```solidity
line#L3:   pragma solidity ^0.8.0;
```

[AllowListed.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowListed.sol):

```solidity
line#L3:   pragma solidity ^0.8.0;
```

[Executor.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol):

```solidity
line#L3:   pragma solidity ^0.8.0;
```

[Storage.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Storage.sol):

```solidity
line#L3:   pragma solidity ^0.8.0;
```

[AllowList.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol):

```solidity
line#L3:   pragma solidity ^0.8.0;
```

[DiamondInit.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondInit.sol):

```solidity
line#L3:   pragma solidity ^0.8.0;
```

[L1ERC20Bridge.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol):

```solidity
line#L3:   pragma solidity ^0.8.0;
```

[L2StandardERC20.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol):

```solidity
line#L3:   pragma solidity ^0.8.0;
```

[Getters.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Getters.sol):

```solidity
line#L3:   pragma solidity ^0.8.0;
```

[ReentrancyGuard.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/ReentrancyGuard.sol):

```solidity
line#L3:   pragma solidity ^0.8.0;
```

### Missing natspec comments

https://docs.soliditylang.org/en/develop/natspec-format.html

[L2ContractHelper.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/L2ContractHelper.sol):

```solidity
line#L1:   // SPDX-License-Identifier: MIT OR Apache-2.0
```

[UncheckedMath.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/libraries/UncheckedMath.sol):

```solidity
line#L1:   // SPDX-License-Identifier: MIT OR Apache-2.0
```

[IZkSync.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IZkSync.sol):

```solidity
line#L1:   // SPDX-License-Identifier: MIT OR Apache-2.0
```

[IDiamondCut.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol):

```solidity
line#L1:   // SPDX-License-Identifier: MIT OR Apache-2.0
```

[IL2EthInitializable.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/interfaces/IL2EthInitializable.sol):

```solidity
line#L1:   // SPDX-License-Identifier: MIT OR Apache-2.0
```

[IL2StandardToken.sol](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/interfaces/IL2StandardToken.sol):

```solidity
line#L1:   // SPDX-License-Identifier: MIT OR Apache-2.0
```
