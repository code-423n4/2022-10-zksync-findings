

## 1. Splitting `require()` statements that use && saves gas

Instead of using the && operator in a single require statement to check multiple conditions,use multiple require statements with 1 condition per require statement.

See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper

```
96	require(
97		callersLength == _targets.length &&
98		callersLength == _functionSigs.length &&
99		callersLength == _enables.length,
100		"yw"
101	);
```
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol
```
65	require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
```
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/L2ContractHelper.sol


## 2. Usage of `uint/int` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed.


## 3. Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be `abstract` and the function signatures be added without any default implementation. If the block is an empty `if`-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...}` => `if(!x){if(y){...}else{...}}`). Empty `receive()`/`fallback()` payable functions that are not used, can be removed to save deployment gas.

```
15	constructor() reentrancyGuardInitializer {}
```
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/DiamondInit.sol
```
39	constructor() initializer {}
```
https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol


## 4. Constructor parameters should be avoided when possible

Constructor parameters are expensive. The contract deployment will be cheaper in gas if they are hard coded instead of using constructor parameters.


```
30	constructor(address _l1Bridge) {
31		l1Bridge = _l1Bridge;
```
https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2ETHBridge.sol
```
48	constructor(IMailbox _mailbox, IAllowList _allowList) reentrancyGuardInitializer {
49		zkSyncMailbox = _mailbox;
50		allowList = _allowList;
```
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/bridge/L1EthBridge.sol
```
32	constructor(address _owner) {
33		require(_owner != address(0), "kq");
34		owner = _owner;
```
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol




