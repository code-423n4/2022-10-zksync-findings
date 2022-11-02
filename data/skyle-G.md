GAS-1 Use shift Right/Left instead of division/multiplication if possible
ethereum/contracts/zksync/libraries/Merkle.sol
Line 34 _index /= 2;

GAS-2 require() or revert() statements that check input arguments should be at the top of the function
/blob/main/ethereum/contracts/zksync/DiamondProxy.sol
Line24 require(msg.data.length >= 4 || msg.data.length == 0, "Ut");