1) 
L2ContractHelper.sol

library L2ContractHelper {

bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");

function sendMessageToL1(bytes memory _message) internal returns (bytes32) {
        return L2_MESSENGER.sendToL1(_message);
    }

When we create constant we already know the string that we need hash. Instead of using keccaks26 function we can directly apply the hash value. So we can reduce some gas fee. since the hash string is static so we don't want use hash function. 


2)

L2StandardERC20.sol

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


in bridgeInitialize function implementations  _data declared as memory 

L2ERC20Bridge.sol

function _deployL2Token(address _l1Token, bytes calldata _data) internal returns (address) {
        bytes32 salt = _getCreate2Salt(_l1Token);

        BeaconProxy l2Token = new BeaconProxy{salt: salt}(address(l2TokenFactory), "");
        L2StandardERC20(address(l2Token)).bridgeInitialize(_l1Token, _data);

        return address(l2Token);
    }

when we calling bridgeInitialize function in L2ERC20Bridge.sol the parameter _data declared as calldata .  _data must be memory 
