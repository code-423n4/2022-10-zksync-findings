L2ERC20Bridge.sol

 /// @dev Deploys and initialize the L2 token for the L1 counterpart

    function _deployL2Token(address _l1Token, bytes calldata _data) internal returns (address) {
        bytes32 salt = _getCreate2Salt(_l1Token);

        BeaconProxy l2Token = new BeaconProxy{salt: salt}(address(l2TokenFactory), "");
        L2StandardERC20(address(l2Token)).bridgeInitialize(_l1Token, _data);

        return address(l2Token);
    }

for _data we can memory instead of calldata. So we can reduce gas fee . _data is temporary variable . We just pass _data value to bridgeInitialize function call 