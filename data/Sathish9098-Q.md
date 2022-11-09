
1) IN THE bridgeInitialize IMPLEMENTATION FUNCTION _data DECLARED AS memory. IN THE CALLER FUNCTION _data DECLARED AS calldata. IN CALLER FUNCTION _data PARAMETER SHOULD BE memory INSTEAD OF calldata 


File :     2022-10-zksync/zksync/contracts/bridge/L2StandardERC20.sol

43:      function bridgeInitialize(address _l1Address, bytes memory _data) external initializer {

File :   2022-10-zksync/zksync/contracts/bridge/L2ERC20Bridge.sol

70 :     function _deployL2Token(address _l1Token, bytes calldata _data) internal returns (address) {

77 :      L2StandardERC20(address(l2Token)).bridgeInitialize(_l1Token, _data);

-------------------------------------------------------------------------------------------------------------------------------------------

2)   uint256 public totalSupply; AND mapping(address => uint256) public balanceOf; DECLARED BUT NOT USED ANY PLACES. CAN REMOVE THE VARIABLES IF NOT USED.