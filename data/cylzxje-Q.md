
#### [L-01] Missing address(0) checks


#### [N-01] Typo comments
#### [N-02] TODO left in code

---
#### [L-01]  Missing address(0) checks 
zksync/contracts/bridge/L2ETHBridge.sol for these `constructor` / `initialize` inputs
- https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L30
- https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L32-L34

ethereum/contracts/bridge/L1EthBridge.sol
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol#L48
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1ERC20Bridge.sol#L57

ethereum/contracts/zksync/DiamondInit.sol
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol#L26-L36

Recommend adding address(0) checks

#### [N-01]  Typo comments
zksync/contracts/bridge/L2StandardERC20.sol
- https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L42

ethereum/contracts/zksync/DiamondProxy.sol
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondProxy.sol#L7

Recommend fixing `decimls`, `Cotract`

#### [N-02] TODO left in code
ethereum/contracts/zksync/facets/Mailbox.sol
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L94
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L127
- https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L169

Recommend implementing or eliminating all TODOS