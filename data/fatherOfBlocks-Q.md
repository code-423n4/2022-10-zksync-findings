**ethereum/contracts/zksync/facets/base**
- L16/22 - When we use a require and throw an exception it is important to show a understandable message, this is important because it makes the user better understand the reason why it is reverted.

- L12 - It would be preferable that the name of the variable in storage "s" of type AppStorage have a more descriptive name than the letter s.


**ethereum/contracts/zksync/facets/DiamondCut**
- L12 - The contract is not named the same as the file, since there is only one contract, by convention and simplicity when interacting with the contracts it is better that they have the same name.
Specifically, someone who imports DiamondCut will already know that it is in the facet folder, therefore the name "DiamondCutFacet" is not the most appropriate.

- L23/40/55/56/59/65/80/94/106/108/111/112 - When we use a require and throw an exception it is important to show a understandable message, this is important because it makes the user better understand the reason why it is reverted.


**ethereum/contracts/zksync/facets/Executor**
- L28/39/41/43/45/52/53/117/129/136/142/143/145/159/192/193/199/216/237/241/261/265/268/297/337 - When we use a require and throw an exception it is important to show a understandable message, this is important because it makes the user better understand the reason why it is reverted.


**ethereum/contracts/zksync/facets/Mailbox**
- L56/63/66/124 - When we use a require and throw an exception it is important to show a understandable message, this is important because it makes the user better understand the reason why it is reverted.


**ethereum/contracts/zksync/libraries/Diamond.sol**
- L100/126/135/150/156/170/176/214/279/283/287/288 - When we use a require and throw an exception it is important to show a understandable message, this is important because it makes the user better understand the reason why it is reverted.

- L108/109 - The action parameter, which is of the enum Action type, only has 3 values ​​and the three possible values ​​are already validated beforehand, therefore it would not be necessary to create a revert for a fourth non-existent case.


**ethereum/contracts/zksync/libraries/Merkle.sol**
- L23/24/25 - When we use a require and throw an exception it is important to show a understandable message, this is important because it makes the user better understand the reason why it is reverted.


**ethereum/contracts/bridge/L1EthBridge.sol**
- L93/141/145/166/189/205/221/224/238 - When we use a require and throw an exception it is important to show a message and has to be understandable, this is important because it makes the user better understand the reason why it is reverted.


**ethereum/contracts/bridge/L1ERC20Bridge.sol**
- L77/117/207/210/232/249/271/274 - When we use a require and throw an exception it is important to show a message and has to be understandable, this is important because it makes the user better understand the reason why it is reverted.


**ethereum/contracts/bridge/interfaces/IL2Bridge.sol**
- L15/21/23/25 - The IL2Bridge interface is used in the two bridge contracts, but only the finalizeDeposit() function is used, but all the others are not used, so they could be removed.


**ethereum/contracts/common/AllowList.sol**
- L33/38/67/96/155 - When we use a require and throw an exception it is important to show a message and has to be understandable, this is important because it makes the user better understand the reason why it is reverted.


**zksync/contracts/bridge/L2ERC20Bridge.sol**
- L58/63/95 - When we use a require and throw an exception it is important to show a message and has to be understandable, this is important because it makes the user better understand the reason why it is reverted.


**zksync/contracts/bridge/L2ETHBridge.sol**
- L16/19 - There are variables in storage: totalSupply and balanceOf that are not used in the entire contract, if they were necessary in contracts that inherit, they should be created in that contract.

- L49/50/64 - When we use a require and throw an exception it is important to show a message and has to be understandable, this is important because it makes the user better understand the reason why it is reverted.


**zksync/contracts/bridge/L2StandardERC20.sol**
- L44/45/96 - When we use a require and throw an exception it is important to show a message and has to be understandable, this is important because it makes the user better understand the reason why it is reverted.

