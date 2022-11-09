## [G-01] Splitting require() statements that use && saves gas

### IMPACT

Require statements including conditions with the && operator can be broken down in
multiple require statements to save gas.

### POC 


```
        require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
```

### MITIGATION

Breakdown each condition in a separate require
```console
require(version == 1);
require(_bytecodeHash[1] == bytes1(0), "zf");)
``` 


## [G-02] abi.encode() is less efficient than abi.encodePacked()

```
File: ethereum\contracts\bridge\L1ERC20Bridge.sol
82:        bytes memory create2Input = abi.encode(address(this), l2ProxyTokenBytecodeHash, _governor);

168:       data = abi.encode(data1, data2, data3);

File: ethereum\contracts\zksync\DiamondInit.sol
54:         s.storedBlockHashes[0] = keccak256(abi.encode(storedBlockZero));


File: ethereum\contracts\zksync\facets\DiamondCut.sol 27:      s.diamondCutStorage.proposedDiamondCutHash = keccak256(abi.encode(_facetCuts, _initAddress));

File:

381:        return abi.encode(_block.l2LogsTreeRoot, l2ToL1LogsHash, initialStorageChangesHash, repeatedStorageChangesHash);


```




https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L82
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L168
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/DiamondInit.sol#L54
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/DiamondCut.sol#L27
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/facets/Executor.sol#L381


## [G-03] Expressions for constant values such as a call to keccak256(), should use immutable 
### Impact
Expressions for constant values such as a call to keccak256(), should use immutable rather than constant
See [https://github.com/ethereum/solidity/issues/9232](this) issue for a detail description of the issue
Similar bug: https://github.com/code-423n4/2021-11-unlock-findings/issues/238

### POC

File: ethereum\contracts\common\L2ContractHelper.sol
```
41:    bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");

```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/common/L2ContractHelper.sol#L41
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/L2ContractHelper.sol#L24


## [G-04] calldata is more gas efficient than memory

When running a function we could pass the function parameters as calldata or memory for variables such as strings, structs,arrays etc. If we are not modifying the passed parameter we should pass it as calldata because calldata is more gas efficient than memory. Let's see an example:

## POC
File: zksync\contracts\ExternalDecoder.sol

```solidity
10:    function decodeString(bytes memory _input) external pure returns (string memory result) {
        (result) = abi.decode(_input, (string));
12:    }
```
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/zksync/contracts/ExternalDecoder.sol#L10-L12