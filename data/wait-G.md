## use unchecked {} when overflow is always impossible
file: /ethereum/contracts/zksync/libraries/Diamond.sol
* line 94: `for (uint256 i = 0; i < facetCutsLength; ++i) {`
* line 132: `for (uint256 i = 0; i < selectorsLength; ++i) {`
* line 153: `for (uint256 i = 0; i < selectorsLength; ++i) {`
* line 173: `for (uint256 i = 0; i < selectorsLength; ++i) {`

## replace arithmetic with shift operation to save gas
file: ethereum/contracts/zksync/libraries/Merkle.sol
* line 25: `2**pathLength` -> `1 << pathLength`
* line 29: `_index % 2 == 0` -> `_index & 2 == 0`
* line 34: `_index /= 2` -> `_index >>= 2`
