

1)  FOR _data PARAMETER CAN REPLACE calldata WITH memory . _data IS TEMPRORARY VARIABLE. IF WE USING MEMORY CAN REDUCE GAS FEE

Same problem in 3 instances.

File :    2022-10-zksync/zksync/contracts/bridge/L2ERC20Bridge.sol

73 :     function _deployL2Token(address _l1Token, bytes calldata _data) internal returns (address) {

File :   2022-10-zksync/zksync/contracts/bridge/L2ERC20Bridge.sol

55:      bytes calldata _data

File :   2022-10-zksync/zksync/contracts/L2ContractHelper.sol

13 :     bytes calldata _input

------------------------------------------------------------------------------------------------------------------------------------------------------------

2.  FOR CREATE2_PREFIX CAN ASSGING HASH VALUE DIRECTLY. THE INPUT STRING  "zksyncCreate2" IS STATIC. SO DON'T WANT TO USE keccak256 FUNCTION. CAN REDUCE THE MORE GAS FEE. 

File : 2022-10-zksync/zksync/contracts/L2ContractHelper.sol

24:  bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
 
-----------------------------------------------------------------------------------------------------------------------------------------------

3)   CONVENTIONAL_ETH_ADDRESS STATE VARIABLE CAN BE CATCHED . SO CAN AVOID STATE VARIABLES ACCESS. CAN REDUCE THE GAS FEE. 

Same problem in 4 instances 

File : 2022-10-zksync/zksync/contracts/bridge/L2ETHBridge.sol

50 :    require(_l1Token == CONVENTIONAL_ETH_ADDRESS);

54 :    emit FinalizeDeposit(_l1Sender, _l2Receiver, CONVENTIONAL_ETH_ADDRESS, _amount);

64:     require(_l2Token == CONVENTIONAL_ETH_ADDRESS, "zn");

70 :    emit WithdrawalInitiated(msg.sender, _l1Receiver, CONVENTIONAL_ETH_ADDRESS, _amount);

