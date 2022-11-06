### Move all variable declarations prior to any usage of the variable

❌ Variable 'L2StandardERC20.bridgeInitialize(address,bytes).symbolString (contracts/bridge/L2StandardERC20.sol:75)' in L2StandardERC20.bridgeInitialize(address,bytes) (contracts/bridge/L2StandardERC20.sol#43-93) potentially used before declaration: decodedSymbol = symbolString (contracts/bridge/L2StandardERC20.sol#76)
❌ Variable 'L2StandardERC20.bridgeInitialize(address,bytes).nameString (contracts/bridge/L2StandardERC20.sol:69)' in L2StandardERC20.bridgeInitialize(address,bytes) (contracts/bridge/L2StandardERC20.sol#43-93) potentially used before declaration: decodedName = nameString (contracts/bridge/L2StandardERC20.sol#70)
❌ Variable 'L2StandardERC20.bridgeInitialize(address,bytes).decimalsUint8 (contracts/bridge/L2StandardERC20.sol:84)' in L2StandardERC20.bridgeInitialize(address,bytes) (contracts/bridge/L2StandardERC20.sol#43-93) potentially used before declaration: decimals_ = decimalsUint8 (contracts/bridge/L2StandardERC20.sol#86)

### Reentrancy vulnerabilities leading to out-of-order Events

❌ Reentrancy in L2ETHBridge.withdraw(address,address,uint256) (contracts/bridge/L2ETHBridge.sol:59-71):
	• ETH_TOKEN_SYSTEM_CONTRACT_ADDRESS.bridgeBurn(msg.sender,_amount) (contracts/bridge/L2ETHBridge.sol#66)
	• L2ContractHelper.sendMessageToL1(message) (contracts/bridge/L2ETHBridge.sol#68)
	• WithdrawalInitiated(msg.sender,_l1Receiver,CONVENTIONAL_ETH_ADDRESS,_amount) (contracts/bridge/L2ETHBridge.sol#70)
❌ Reentrancy in L2ETHBridge.finalizeDeposit(address,address,address,uint256,bytes) (contracts/bridge/L2ETHBridge.sol:41-55):
	• ETH_TOKEN_SYSTEM_CONTRACT_ADDRESS.bridgeMint(_l2Receiver,_amount) (contracts/bridge/L2ETHBridge.sol#52)
	• FinalizeDeposit(_l1Sender,_l2Receiver,CONVENTIONAL_ETH_ADDRESS,_amount) (contracts/bridge/L2ETHBridge.sol#54)
❌ Reentrancy in L2ERC20Bridge.withdraw(address,address,uint256) (contracts/bridge/L2ERC20Bridge.sol:87-101):
	• IL2StandardToken(_l2Token).bridgeBurn(msg.sender,_amount) (contracts/bridge/L2ERC20Bridge.sol#92)
	• L2ContractHelper.sendMessageToL1(message) (contracts/bridge/L2ERC20Bridge.sol#98)
	• WithdrawalInitiated(msg.sender,_l1Receiver,_l2Token,_amount) (contracts/bridge/L2ERC20Bridge.sol#100)
❌ Reentrancy in L2ERC20Bridge.finalizeDeposit(address,address,address,uint256,bytes) (contracts/bridge/L2ERC20Bridge.sol:50-70):
	• deployedToken = _deployL2Token(_l1Token,_data) (contracts/bridge/L2ERC20Bridge.sol#62)
	• l2Token = new BeaconProxy(address(l2TokenFactory),) (contracts/bridge/L2ERC20Bridge.sol#76)
	• L2StandardERC20(address(l2Token)).bridgeInitialize(_l1Token,_data) (contracts/bridge/L2ERC20Bridge.sol#77)
	• IL2StandardToken(expectedL2Token).bridgeMint(_l2Receiver,_amount) (contracts/bridge/L2ERC20Bridge.sol#67)
	• FinalizeDeposit(_l1Sender,_l2Receiver,expectedL2Token,_amount) (contracts/bridge/L2ERC20Bridge.sol#69)

### Missing Zero Address Validation
❌ L2ETHBridge.constructor(address)._l1Bridge (contracts/bridge/L2ETHBridge.sol:30) lacks a zero-check on :
	• l1Bridge = _l1Bridge (contracts/bridge/L2ETHBridge.sol#31)
❌ L2ERC20Bridge.constructor(address,bytes32,address)._l1Bridge (contracts/bridge/L2ERC20Bridge.sol:32) lacks a zero-check on :
	• l1Bridge = _l1Bridge (contracts/bridge/L2ERC20Bridge.sol#36)

### Incorrect Solidity version
❌ Pragma version^0.8.0 (contracts/ExternalDecoder.sol:3)
❌ Pragma version^0.8.0 (contracts/L2ContractHelper.sol:3)
❌ Pragma version^0.8.0 (contracts/bridge/L2ERC20Bridge.sol:3) 
❌ Pragma version^0.8.0 (contracts/bridge/L2ETHBridge.sol:3) 
❌ Pragma version^0.8.0 (contracts/bridge/L2StandardERC20.sol:3)
❌ Pragma version^0.8.0 (contracts/bridge/interfaces/IL1Bridge.sol:3) 
❌ Pragma version^0.8.0 (contracts/bridge/interfaces/IL2Bridge.sol:3)
❌ Pragma version^0.8.0 (contracts/bridge/interfaces/IL2EthInitializable.sol:3)
❌ Pragma version^0.8.0 (contracts/bridge/interfaces/IL2StandardToken.sol:3)


Use a simple pragma version that allows any of these versions.
Consider using the latest version of Solidity for testing.
