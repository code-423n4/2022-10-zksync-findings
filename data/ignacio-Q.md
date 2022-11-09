 ABI.ENCODEPACKED() SHOULD NOT BE USED WITH DYNAMIC TYPES WHEN PASSING THE RESULT TO A HASH FUNCTION SUCH AS KECCAK256()

 
If you use keccak256(abi.encodePacked(a, b)) and both a and b are dynamic types, it is easy to craft collisions in the hash value by moving parts of a into b and vice-versa. More specifically, abi.encodePacked("a", "bc") == abi.encodePacked("ab", "c").

 two or more dynamic types are passed to abi.encodePacked. Moreover, these dynamic values are user-specified function arguments in external functions, meaning anyone can directly specify the value of these arguments when calling the function. The signedOnly modifier is supposed to protect functions to permit only function arguments that have been properly signed to be passed to the function logic, but because a collision can be created, the modifier can be bypassed for certain select inputs that result in the same encodePacked value.

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol#L59
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L283 
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/Executor.sol#L364-L373