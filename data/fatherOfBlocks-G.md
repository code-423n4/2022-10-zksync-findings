**ethereum/contracts/zksync/DiamondProxy**
- L13/24/29/30 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L24 - When a variable is used more than once, it is less expensive to create a variable in length memory and use that variable.


**ethereum/contracts/zksync/facets/base**
- L16/22 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.


**ethereum/contracts/zksync/facets/DiamondCut**
- L16 - It is less expensive to validate that uint != 0 than to validate uint > 0

- L23/40/55/56/59/65/80/94/106/108/111/112 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L52/55 - It is not necessary to create a variable and set a value when we want to set its default value, this is so since when creating the variable it already has that value.


**ethereum/contracts/zksync/facets/Executor**
- L28/39/41/43/45/52/53/117/129/136/142/143/145/159/192/193/199/216/237/241/261/265/268/297/337 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L111/162/180/210/240 - It is not necessary to create a variable and set a value when we want to set its default value, this is so since when creating the variable it already has that value.

- L111 - When the length of an array is used more than once, it is less expensive to create a variable in length memory and use that variable.

- L377/378/379/381 - When a variable is only used once, it is not necessary to create a variable in memory, the operation or function call can be used directly.


**ethereum/contracts/zksync/facets/Getters**
- L163 - It is not necessary to create a variable and set a value when we want to set its default value, this is so since when creating the variable it already has that value.


**ethereum/contracts/zksync/facets/Mailbox**
- L56/63/66/124 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L223 - It is not necessary to create a variable and set a value when we want to set its default value, this is so since when creating the variable it already has that value.


**ethereum/contracts/zksync/libraries/Diamond.sol**
- L100/126/135/150/156/170/176/214/279/283/287/288 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L94/132/153/173 - It is not necessary to create a variable and set a value when we want to set its default value, this is so since when creating the variable it already has that value.

- L94/132/153/173 - Being impossible for i++ to be > uint256, therefore it can be unchecked

- L100 - It is less expensive to validate that uint != 0 than to validate uint > 0


**ethereum/contracts/zksync/libraries/Merkle.sol**
- L23/24/25 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L23 - It is less expensive to validate that uint != 0 than to validate uint > 0

- L28 - It is not necessary to create a variable and set a value when we want to set its default value, this is so since when creating the variable it already has that value.


**ethereum/contracts/bridge/L1EthBridge.sol**
- L93/141/145/166/189/205/221/224/238 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L33/57 - It is not necessary to create a variable and set a value when we want to set its default value, this is so since when creating the variable it already has that value.


**ethereum/contracts/bridge/L1ERC20Bridge.sol**
- L77/117/207/210/232/249/271/274 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L117/210 - It is less expensive to validate that uint != 0 than to validate uint > 0

- L81 - It is not necessary to create a variable and set a value when we want to set its default value, this is so since when creating the variable it already has that value.

- L145 - The balanceAfter operation is executed - balanceBefore which can be unchecked, since balanceAfter is >= balanceBefore.


**ethereum/contracts/common/AllowList.sol**
- L33/38/67/96/155 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L69/103 - It is not necessary to create a variable and set a value when we want to set its default value, this is so since when creating the variable it already has that value.


**zksync/contracts/bridge/L2ERC20Bridge.sol**
- L58/63/95 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L19/23/26 - The variables in storage l1Bridge, l2TokenFactory and l2TokenProxyBytecodeHash should be immutable as they are only set in the constructor and then don't have a setter.

- L62/63/74/76/97/98/114/115/118 - When a variable is only used once, it is not necessary to create a variable in memory, you can directly use the operation or the function call .


**zksync/contracts/bridge/L2ETHBridge.sol**
- L49/50/64 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L22/31 - The variables in storage l1Bridge should be immutable since it is only set in the constructor and then does not have a setter.

- L28 - It is not necessary to create a variable and set a value when we want to set its default value, this is so since when creating the variable it already has that value.


**zksync/contracts/bridge/L2StandardERC20.sol**
- L44/45/96 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.
