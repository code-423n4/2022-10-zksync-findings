## 2**<N> - 1 SHOULD BE RE-WRITTEN AS TYPE(UINT<N>).MAX

Instead of `2**16` write `type(uint16).max`

Instances:
-L2ContractHelper.sol line 53

## EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

-ethereum L2ContractHelper.sol line 41
-zksync L2ContractHelper.sol line 24


## STATE VARIABLE VISIBILITY IS NOT SET
Visibility is not set for the <> state variable.

-L2ERC20Bridge.sol line 26











