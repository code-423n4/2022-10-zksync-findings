-> USE X = X + Y ITS CHEAPER THAN X += Y (same for X = X - Y IS CHEAPER THAN X -= Y); SO TRY TO AVOID +=/-= AND USE X=X+Y OR X=X-Y INSTEAD

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#:~:text=s.diamondCutStorage.currentProposalId%20%2B%3D%201%3B

->SPLIT REQUIRE () STATEMENTS, IF THEY USE &&, TO SAVE GAS.

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/AllowList.sol#:~:text=_callers.length%3B-,require(,)%3B,-//%20The%20size%20of

-> DONT USE INTS/UINTS SMALLER THAN 32BYTE
If you use elements smaller than 32 bytes, the usage of gas from your contract could be higher. Thats because the EVM operates on 32 bytes at the moment. When there is an element smaller than 32 bytes, the EVM needs to do more to reduce the size from 32 Bytes to the specific size. 

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#:~:text=struct%20L2Log%20%7B-,uint8,-l2ShardId%3B
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#:~:text=bool%20isService%3B-,uint16,-txNumberInBlock%3B
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#:~:text=struct%20L2Message%20%7B-,uint16,-txNumberInBlock%3B
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/interfaces/IAllowList.sol#:~:text=address%20_target%2C-,bytes4,-_functionSig%2C
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/common/interfaces/IAllowList.sol#:~:text=calldata%20_targets%2C-,bytes4%5B%5D,-calldata%20_functionSigs%2C


->WHEN YOU USE BOOLS FOR STORAGE IT WILL COST MORE GAS AND INCURS OVERHEAD

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#:~:text=mapping(address%20%3D%3E%20bool)%20securityCouncilMembers%3B
https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/Storage.sol#:~:text=mapping(address%20%3D%3E%20bool)%20validators%3B
