L2ContractHelper.sol #L24

library L2ContractHelper {
    bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
 function sendMessageToL1(bytes memory _message) internal returns (bytes32) {
        return L2_MESSENGER.sendToL1(_message);
    }

FINDINGS :

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/L2ContractHelper.sol#L24

For CREATE2_PREFIX we can directly assign hash value instead of Calling the keccak256 function. If we apply hash value directly we can save our gas fee. The string is static so need to calculate using keccak256 functions. 

----------------------------------------------------------------------------------------------------------------------------------------------------------------
2)
L2StandardERC20.sol #L43

function bridgeInitialize(address _l1Address, bytes memory _data) external initializer {

        require(l1Address == address(0), "in5"); // Is already initialized
        require(_l1Address != address(0), "in6"); // Should be non-zero address
        l1Address = _l1Address;

        l2Bridge = msg.sender;

        // We parse the data exactly as they were created on the L1 bridge
        (bytes memory nameBytes, bytes memory symbolBytes, bytes memory decimalsBytes) = abi.decode(
            _data,
            (bytes, bytes, bytes)
        );

FINDINGS : 

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L43

in bridgeInitialize function implementations  _data declared as memory 

L2ERC20Bridge.sol #L73

function _deployL2Token(address _l1Token, bytes calldata _data) internal returns (address) {
        bytes32 salt = _getCreate2Salt(_l1Token);

        BeaconProxy l2Token = new BeaconProxy{salt: salt}(address(l2TokenFactory), "");
        L2StandardERC20(address(l2Token)).bridgeInitialize(_l1Token, _data);

        return address(l2Token);
    }

FINDINGS :

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ERC20Bridge.sol#L73

when calling bridgeInitialize function in L2ERC20Bridge.sol the function parameter _data declared as calldata  .  _data must be memory 
