## 1. Custom error are cheaper than string messages

_ethereum/contracts/zksync/facets/Base.sol:_ [L16](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Base.sol#L16)
[L22](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Base.sol#L22)

_ethereum/contracts/zksync/libraries/Diamond.sol:_ [L100](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L100)
[L109](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L109)
[L126](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L126)
[L135](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L135)
[L150](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L150)
[L170](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L170)
[L176](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L176)
[L214](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L214)
[L279](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L279)

_ethereum/contracts/common/L2ContractHelper.sol:_ [L50](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol#L50)
[L53](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol#L53)
[L65](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol#L65)
[L67](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol#L67)

_ethereum/contracts/common/ReentrancyGuard.sol:_ [L55](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/ReentrancyGuard.sol#L55)
[L72](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/ReentrancyGuard.sol#L72)

_ethereum/contracts/common/AllowList.sol:_ [L33](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L33)
[L38](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L38)
[L67](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L67)
[L155](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L155)

_zksync/contracts/bridge/L2ETHBridge.sol:_ [L49](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol#L49)
[L64](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol#L64)

_ethereum/contracts/bridge/L1EthBridge.sol:_ [L93](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L93)
[L141](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L141)
[L166](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L166)
[L205](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L205)

_ethereum/contracts/zksync/libraries/Merkle.sol:_ [L23](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Merkle.sol#L23)
[L24](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Merkle.sol#L24)
[L25](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Merkle.sol#L25)

_ethereum/contracts/zksync/facets/Executor.sol:_ [L28](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L28)
[L41](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L41)
[L52](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L52)
[L57-L61](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L57-L61)
[L117](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L117)
[L129](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L129)
[L145](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L145)
[L159](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L159)
[L192](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L192)
[L193-L196](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L193-L196)
[L199](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L199)
[L237](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L237)
[L241-L245](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L241-L245)
[L261](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L261)
[L265](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L265)
[L337](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L337)

_ethereum/contracts/bridge/L1ERC20Bridge.sol:_ [L117](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L117)
[L207](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L207)
[L210](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L210)
[L232](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L232)
[L271](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L271)
[L274](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L274)

_ethereum/contracts/zksync/facets/Governance.sol:_ [L30](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L30)

_zksync/contracts/bridge/L2ERC20Bridge.sol:_ [L58](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L58)
[L63](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L63)
[L95](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L95)

_ethereum/contracts/zksync/facets/DiamondCut.sol:_ [L40](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L40)
[L55](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L55)
[L59-L63](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L59-L63)
[L65](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L65)
[L80](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L80)
[L94](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L94)
[L106](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L106)
[L111](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L111)
[L112](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L112)

_ethereum/contracts/zksync/facets/Getters.sol:_ [L148](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Getters.sol#L148)

_ethereum/contracts/zksync/facets/Mailbox.sol:_ [L56](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol#L56)
[L63](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol#L63)
[L124](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol#L124)

_ethereum/contracts/zksync/libraries/PriorityQueue.sol:_ [L64](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L64)
[L72](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L72)

_ethereum/contracts/zksync/DiamondProxy.sol:_ [L13](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondProxy.sol#L13)
[L24](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondProxy.sol#L24)
[L29](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondProxy.sol#L29)

_zksync/contracts/bridge/L2StandardERC20.sol:_ [L44](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L44)
[L45](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L45)

_ethereum/contracts/common/AllowListed.sol:_ [L15](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowListed.sol#L15)

## 2. Use `immutable` instead of `constant` for the following to save runtime gas

_ethereum/contracts/common/L2ContractHelper.sol:_ [L41](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol#L41)

_zksync/contracts/L2ContractHelper.sol:_ [L24](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/L2ContractHelper.sol#L24)

## 3. Consider marking functions as `payable` if there is no risk of sending value through them

### This change will save gas each time a function is called

_ethereum/contracts/common/AllowList.sol:_ [L57](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L57)
[L140](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L140)
[L153](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/AllowList.sol#L153)

_ethereum/contracts/zksync/facets/Governance.sol:_ [L28](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Governance.sol#L28)

_zksync/contracts/bridge/L2StandardERC20.sol:_ [L102](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L102)
[L109](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L109)

## 4. Function that are called only once can be inlined in the calling function

### This change will save around 30 gas units

_ethereum/contracts/zksync/libraries/Diamond.sol:_ [L119](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L119)
[L143](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L143)
[L167](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L167)
[L277](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L277)

_ethereum/contracts/common/ReentrancyGuard.sol:_ [L43](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/ReentrancyGuard.sol#L43)

## 5. When comparing variables of type `uint`, use `require(x != 0)` instead of `require(x > 0)`

_ethereum/contracts/bridge/L1ERC20Bridge.sol:_ [L117](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L117)
[L210](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L210)

_ethereum/contracts/zksync/libraries/Merkle.sol:_ [L23](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Merkle.sol#L23)

## 6. Use bitshifting instead of multiplication and divison

_ethereum/contracts/zksync/libraries/Merkle.sol:_ [L25](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Merkle.sol#L25)

_ethereum/contracts/zksync/Config.sol:_ [L23](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/Config.sol#L23)

_ethereum/contracts/common/L2ContractHelper.sol:_ [L53](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol#L53)
[L72](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol#L72)

## 7. Calldata is cheaper than memory for function input

_zksync/contracts/bridge/L2StandardERC20.sol:_ [L43](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L43)

_ethereum/contracts/common/L2ContractHelper.sol:_ [L43](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol#L43)
[L47](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/L2ContractHelper.sol#L47)

_ethereum/contracts/bridge/L1ERC20Bridge.sol:_ [L260](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L260)

_ethereum/contracts/common/libraries/UnsafeBytes.sol:_ [L18](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/libraries/UnsafeBytes.sol#L18)
[L25](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/libraries/UnsafeBytes.sol#L25)
[L32](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/common/libraries/UnsafeBytes.sol#L32)

_ethereum/contracts/zksync/libraries/Diamond.sol:_ [L89](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L89)
[L121](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L121)
[L145](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L145)
[L277](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L277)

_ethereum/contracts/zksync/facets/Mailbox.sol:_ [L43](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol#L43)
[L53](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol#L53)

_zksync/contracts/ExternalDecoder.sol:_ [L10](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/ExternalDecoder.sol#L10)
[L15](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/ExternalDecoder.sol#L15)

_ethereum/contracts/bridge/L1EthBridge.sol:_ [L214](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/bridge/L1EthBridge.sol#L214)

_ethereum/contracts/zksync/libraries/PriorityQueue.sol:_ [L54](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L54)

_zksync/contracts/L2ContractHelper.sol:_ [L26](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/L2ContractHelper.sol#L26)

_ethereum/contracts/zksync/facets/Executor.sol:_ [L23](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L23)
[L152](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L152)
[L190](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L190)
[L278](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L278)
[L385](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L385)

## 8. Use `constant` and `immutable` for constants

_zksync/contracts/bridge/L2ETHBridge.sol:_ [L16](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol#L16)
[L22](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ETHBridge.sol#L22)

_ethereum/contracts/zksync/facets/Base.sol:_ [L12](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Base.sol#L12)

_zksync/contracts/bridge/L2ERC20Bridge.sol:_ [L19](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L19)
[L23](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L23)
[L26](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L26)

_zksync/contracts/bridge/L2StandardERC20.sol:_ [L24](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L24)
[L32](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L32)
[L35](https://github.com/code-423n4/2022-10-zksync/tree/main/zksync/contracts/bridge/L2StandardERC20.sol#L35)

## 9. Place `i++` in an `unchecked` blocks in for-loops

_ethereum/contracts/zksync/libraries/Diamond.sol:_ [L94](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L94)
[L132](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L132)
[L153](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/libraries/Diamond.sol#L153)

## 10. Use `x < y + 1` in stead of `x <= y`

_ethereum/contracts/zksync/facets/DiamondCut.sol:_ [L49](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L49)
[L52](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L52)

_ethereum/contracts/zksync/facets/Executor.sol:_ [L51](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L51)
[L216](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L216)
[L268](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Executor.sol#L268)

_ethereum/contracts/zksync/DiamondProxy.sol:_ [L24](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/DiamondProxy.sol#L24)

_ethereum/contracts/zksync/facets/Mailbox.sol:_ [L56](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol#L56)
[L124](https://github.com/code-423n4/2022-10-zksync/tree/main/ethereum/contracts/zksync/facets/Mailbox.sol#L124)
