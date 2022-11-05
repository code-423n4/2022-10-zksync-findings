L2ERC20Bridge.sol #L73   

1)   for _data we can use memory instead of calldata

 /// @dev Deploys and initialize the L2 token for the L1 counterpart

 function _deployL2Token(address _l1Token, bytes calldata _data) internal returns (address) {

        bytes32 salt = _getCreate2Salt(_l1Token);
        BeaconProxy l2Token = new BeaconProxy{salt: salt}(address(l2TokenFactory), "");
        L2StandardERC20(address(l2Token)).bridgeInitialize(_l1Token, _data);

        return address(l2Token);
    }

FINDINGS : 

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L73

SOLUTIONS:

for _data we can use memory instead of calldata. So we can reduce gas fee . _data is temporary variable . We just pass _data value to bridgeInitialize function call 

------------------------------------------------------------------------------------------------------------------------------------------------------------

2. For CREATE2_PREFIX we can directly assign hash value instead of Calling the keccak256 function

L2ContractHelper.sol #L24

library L2ContractHelper {
    bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
 function sendMessageToL1(bytes memory _message) internal returns (bytes32) {
        return L2_MESSENGER.sendToL1(_message);
    }

FINDINGS :

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/L2ContractHelper.sol#L24

SOLUTIONS:

For CREATE2_PREFIX we can directly assign hash value instead of Calling the keccak256 function. If we apply hash value directly we can save our gas fee. The string is static so need to calculate using keccak256 functions. 
----------------------------------------------------------------------------------------------------------------------------
3)   For _data variable  WE CAN USE memory INSTEAD OF calldata

L2ERC20Bridge.sol #L13

function finalizeDeposit(
        address _l1Sender,
        address _l2Receiver,
        address _l1Token,
        uint256 _amount,

        bytes calldata _data


    ) external override {
        // Only L1 bridge counterpart can initiate and finalize the deposit
        require(msg.sender == l1Bridge, "mq");
 address expectedL2Token = l2TokenAddress(_l1Token);
        if (l1TokenAddress[expectedL2Token] == address(0)) {
            address deployedToken = _deployL2Token(_l1Token, _data);
            require(deployedToken == expectedL2Token, "mt");
            l1TokenAddress[expectedL2Token] = _l1Token;
        }

        IL2StandardToken(expectedL2Token).bridgeMint(_l2Receiver, _amount);

        emit FinalizeDeposit(_l1Sender, _l2Receiver, expectedL2Token, _amount);
    }

FINDINGS : 

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/L2ContractHelper.sol#L13

SOLUTIONS:

Line13 For _data variable  WE CAN USE memory INSTEAD OF calldata . For using memory we can reduce our gas fee 

-----------------------------------------------------------------------------------------------------------------------------------------------

4)   CONVENTIONAL_ETH_ADDRESS needs to CATCHED

L2ETHBridge.sol #L50, #L54 #64 #70

function finalizeDeposit(
        address _l1Sender,
        address _l2Receiver,
        address _l1Token,
        uint256 _amount,
        bytes calldata // _data
    ) external {
        // Only L1 bridge counterpart can initiate and finalize the deposit
        require(msg.sender == l1Bridge, "ni");
        require(_l1Token == CONVENTIONAL_ETH_ADDRESS);

        ETH_TOKEN_SYSTEM_CONTRACT_ADDRESS.bridgeMint(_l2Receiver, _amount);

        emit FinalizeDeposit(_l1Sender, _l2Receiver, CONVENTIONAL_ETH_ADDRESS, _amount);
    }

    /// @dev Initiate withdrawal ethers from L2 contract to the L1
    /// NOTE: In order to get funds on L1, receiver should finalise deposit on L1 counterpart
    function withdraw(
        address _l1Receiver,
        address _l2Token,
        uint256 _amount
    ) external override {
        require(_l2Token == CONVENTIONAL_ETH_ADDRESS, "zn");

        ETH_TOKEN_SYSTEM_CONTRACT_ADDRESS.bridgeBurn(msg.sender, _amount);
        bytes memory message = _getL1WithdrawMessage(_l1Receiver, _amount);
        L2ContractHelper.sendMessageToL1(message);

        emit WithdrawalInitiated(msg.sender, _l1Receiver, CONVENTIONAL_ETH_ADDRESS, _amount);
    }

FINDINGS : 

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L50
https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L54
https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L54
https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol#L54

SOLUTION:

CONVENTIONAL_ETH_ADDRESS needs to CATCHED . finalizeDeposit and Withdraw Functions access the CONVENTIONAL_ETH_ADDRESS state variable in multiple times. It would cost more gas fee in every call  . Instead of direct call we can catch in local .