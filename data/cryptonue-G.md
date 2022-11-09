# Splitting require() statements that use && saves gas

```solidity
File: AllowList.sol
096:         require(
097:             callersLength == _targets.length &&
098:                 callersLength == _functionSigs.length &&
099:                 callersLength == _enables.length,
100:             "yw"
101:         ); // The size of arrays should be equal
```

# Internal functions only called once can be inlined to save gas
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls

`_depositFunds()`, `_getDepositL2Calldata()` internal function only called once
```solidity
File: L1ERC20Bridge.sol
136:     function _depositFunds(
137:         address _from,
138:         IERC20 _token,
139:         uint256 _amount
140:     ) internal returns (uint256) {
141:         uint256 balanceBefore = _token.balanceOf(address(this));
142:         _token.safeTransferFrom(_from, address(this), _amount);
143:         uint256 balanceAfter = _token.balanceOf(address(this));
144: 
145:         return balanceAfter - balanceBefore;
146:     }
147: 
148:     /// @dev Generate a calldata for calling the deposit finalization on the L2 bridge contract
149:     function _getDepositL2Calldata(
150:         address _l1Sender,
151:         address _l2Receiver,
152:         address _l1Token,
153:         uint256 _amount
154:     ) internal view returns (bytes memory txCalldata) {
155:         bytes memory gettersData = _getERC20Getters(_l1Token);
156: 
157:         txCalldata = abi.encodeCall(
158:             IL2Bridge.finalizeDeposit,
159:             (_l1Sender, _l2Receiver, _l1Token, _amount, gettersData)
160:         );
161:     }
```