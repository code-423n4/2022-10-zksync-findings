# 2022-10-ZKSYNC

## Low Risk And Non-Critical Issues

### Events not emmited on important state changes

Emmiting events is recommended each time when a state variable's value is being changed or just some critical event for the contract has occurred. It also helps off-chain monitoring of the contract's state.

_There are **2** instances of this issue:_

```solidity
File: ethereum/contracts/bridge/L1ERC20Bridge.sol

71:   function initialize(
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol

```solidity
File: ethereum/contracts/bridge/L1EthBridge.sol

56:   function initialize(bytes calldata _l2BridgeBytecode) external reentrancyGuardInitializer {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol

### `public` functions not called by the contract should be declared `external` instead

_There are **7** instances of this issue:_

```solidity
File: ethereum/contracts/bridge/L1ERC20Bridge.sol

274:  require(bytes4(functionSignature) == this.finalizeWithdrawal.selector, "nt");

282:  function l2TokenAddress(address _l1Token) public view returns (address) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol

```solidity
File: ethereum/contracts/bridge/L1EthBridge.sol

243:  function l2TokenAddress(address) public pure returns (address) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol

```solidity
File: ethereum/contracts/zksync/facets/Mailbox.sol

89:   function l2TransactionBaseCost(
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol

```solidity
File: zksync/contracts/bridge/L2ETHBridge.sol

79:   function l2TokenAddress(address) public pure returns (address) {

84:   function l1TokenAddress(address) public pure override returns (address) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol

```solidity
File: zksync/contracts/bridge/L2StandardERC20.sol

126:  function decimals() public view override returns (uint8) {
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol

### Left ToDos

Code architecture, incentives and error handling questions should be resolved before deployment

_There are **1** instances of this issue:_

```solidity
File: ethereum/contracts/zksync/interfaces/IExecutor.sol

56:   /// TODO: The verifier integration is not finished yet, change the structure for compatibility later
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IExecutor.sol

### Non-library/interface files should use fixed compiler versions, not floating ones

_There are **18** instances of this issue:_

```solidity
File: ethereum/contracts/bridge/L1ERC20Bridge.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol

```solidity
File: ethereum/contracts/bridge/L1EthBridge.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol

```solidity
File: ethereum/contracts/common/AllowList.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol

```solidity
File: ethereum/contracts/common/AllowListed.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowListed.sol

```solidity
File: ethereum/contracts/common/ReentrancyGuard.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/ReentrancyGuard.sol

```solidity
File: ethereum/contracts/zksync/Config.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Config.sol

```solidity
File: ethereum/contracts/zksync/DiamondInit.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondInit.sol

```solidity
File: ethereum/contracts/zksync/DiamondProxy.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondProxy.sol

```solidity
File: ethereum/contracts/zksync/Storage.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Storage.sol

```solidity
File: ethereum/contracts/zksync/facets/Base.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Base.sol

```solidity
File: ethereum/contracts/zksync/facets/DiamondCut.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol

```solidity
File: ethereum/contracts/zksync/facets/Executor.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol

```solidity
File: ethereum/contracts/zksync/facets/Getters.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Getters.sol

```solidity
File: ethereum/contracts/zksync/facets/Governance.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol

```solidity
File: ethereum/contracts/zksync/facets/Mailbox.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol

```solidity
File: zksync/contracts/bridge/L2ERC20Bridge.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol

```solidity
File: zksync/contracts/bridge/L2ETHBridge.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol

```solidity
File: zksync/contracts/bridge/L2StandardERC20.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol

### Natspec is missing

_There are **6** instances of this issue:_

```solidity
File: ethereum/contracts/common/libraries/UncheckedMath.sol

1:    // SPDX-License-Identifier: MIT OR Apache-2.0
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/libraries/UncheckedMath.sol

```solidity
File: ethereum/contracts/zksync/interfaces/IDiamondCut.sol

1:    // SPDX-License-Identifier: MIT OR Apache-2.0
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol

```solidity
File: ethereum/contracts/zksync/interfaces/IZkSync.sol

1:    // SPDX-License-Identifier: MIT OR Apache-2.0
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IZkSync.sol

```solidity
File: zksync/contracts/L2ContractHelper.sol

1:    // SPDX-License-Identifier: MIT OR Apache-2.0
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/L2ContractHelper.sol

```solidity
File: zksync/contracts/bridge/interfaces/IL2EthInitializable.sol

1:    // SPDX-License-Identifier: MIT OR Apache-2.0
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/interfaces/IL2EthInitializable.sol

```solidity
File: zksync/contracts/bridge/interfaces/IL2StandardToken.sol

1:    // SPDX-License-Identifier: MIT OR Apache-2.0
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/interfaces/IL2StandardToken.sol

### Event is missing `indexed` fields

_There are **5** instances of this issue:_

```solidity
File: ethereum/contracts/bridge/interfaces/IL1Bridge.sol

11:   event WithdrawalFinalized(address indexed to, address indexed l1Token, uint256 amount);

13:   event ClaimedFailedDeposit(address indexed to, address indexed l1Token, uint256 amount);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/interfaces/IL1Bridge.sol

```solidity
File: ethereum/contracts/zksync/interfaces/IExecutor.sol

85:   event BlocksRevert(uint256 totalBlocksCommitted, uint256 totalBlocksVerified, uint256 totalBlocksExecuted);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IExecutor.sol

```solidity
File: ethereum/contracts/zksync/libraries/Diamond.sol

16:   event DiamondCut(FacetCut[] facetCuts, address initAddress, bytes initCalldata);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol

```solidity
File: zksync/contracts/bridge/L2StandardERC20.sol

12:   event BridgeInitialization(address indexed l1Token, string name, string symbol, uint8 decimals);
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol

### Use a more recent version of solidity

- Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining \ - Use a solidity version of at least 0.8.3 to get cheaper multiple storage reads \ - Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings \ - Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

_There are **39** instances of this issue:_

```solidity
File: ethereum/contracts/bridge/L1ERC20Bridge.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol

```solidity
File: ethereum/contracts/bridge/L1EthBridge.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol

```solidity
File: ethereum/contracts/bridge/interfaces/IL1Bridge.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/interfaces/IL1Bridge.sol

```solidity
File: ethereum/contracts/bridge/interfaces/IL2Bridge.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/interfaces/IL2Bridge.sol

```solidity
File: ethereum/contracts/common/AllowList.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol

```solidity
File: ethereum/contracts/common/AllowListed.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowListed.sol

```solidity
File: ethereum/contracts/common/L2ContractHelper.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol

```solidity
File: ethereum/contracts/common/ReentrancyGuard.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/ReentrancyGuard.sol

```solidity
File: ethereum/contracts/common/interfaces/IAllowList.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/interfaces/IAllowList.sol

```solidity
File: ethereum/contracts/common/libraries/UncheckedMath.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/libraries/UncheckedMath.sol

```solidity
File: ethereum/contracts/common/libraries/UnsafeBytes.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/libraries/UnsafeBytes.sol

```solidity
File: ethereum/contracts/zksync/Config.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Config.sol

```solidity
File: ethereum/contracts/zksync/DiamondInit.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondInit.sol

```solidity
File: ethereum/contracts/zksync/DiamondProxy.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondProxy.sol

```solidity
File: ethereum/contracts/zksync/Storage.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Storage.sol

```solidity
File: ethereum/contracts/zksync/facets/Base.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Base.sol

```solidity
File: ethereum/contracts/zksync/facets/DiamondCut.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol

```solidity
File: ethereum/contracts/zksync/facets/Executor.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol

```solidity
File: ethereum/contracts/zksync/facets/Getters.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Getters.sol

```solidity
File: ethereum/contracts/zksync/facets/Governance.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol

```solidity
File: ethereum/contracts/zksync/facets/Mailbox.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol

```solidity
File: ethereum/contracts/zksync/interfaces/IDiamondCut.sol

3:    pragma solidity ^0.8;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IDiamondCut.sol

```solidity
File: ethereum/contracts/zksync/interfaces/IExecutor.sol

3:    pragma solidity ^0.8;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IExecutor.sol

```solidity
File: ethereum/contracts/zksync/interfaces/IGetters.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IGetters.sol

```solidity
File: ethereum/contracts/zksync/interfaces/IGovernance.sol

3:    pragma solidity ^0.8;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IGovernance.sol

```solidity
File: ethereum/contracts/zksync/interfaces/IMailbox.sol

3:    pragma solidity ^0.8;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IMailbox.sol

```solidity
File: ethereum/contracts/zksync/interfaces/IZkSync.sol

3:    pragma solidity ^0.8;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/interfaces/IZkSync.sol

```solidity
File: ethereum/contracts/zksync/libraries/Diamond.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol

```solidity
File: ethereum/contracts/zksync/libraries/Merkle.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Merkle.sol

```solidity
File: ethereum/contracts/zksync/libraries/PriorityQueue.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol

```solidity
File: zksync/contracts/ExternalDecoder.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/ExternalDecoder.sol

```solidity
File: zksync/contracts/L2ContractHelper.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/L2ContractHelper.sol

```solidity
File: zksync/contracts/bridge/L2ERC20Bridge.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol

```solidity
File: zksync/contracts/bridge/L2ETHBridge.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol

```solidity
File: zksync/contracts/bridge/L2StandardERC20.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol

```solidity
File: zksync/contracts/bridge/interfaces/IL1Bridge.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/interfaces/IL1Bridge.sol

```solidity
File: zksync/contracts/bridge/interfaces/IL2Bridge.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/interfaces/IL2Bridge.sol

```solidity
File: zksync/contracts/bridge/interfaces/IL2EthInitializable.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/interfaces/IL2EthInitializable.sol

```solidity
File: zksync/contracts/bridge/interfaces/IL2StandardToken.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/interfaces/IL2StandardToken.sol
