### No. 1
The critical parameters in `initialize(...)` are not set safely:

 - `s.governor` should be set to `msg.sender`, because a wrong governor address will result in loss of access to all other parts, and later changing the governor to the correct address.
 - `_l2BootloaderBytecodeHash` should be validated like `L2ContractHelper.validateBytecodeHash(_l2BootloaderBytecodeHash)` as in `GovernanceFacet`
 - `_l2DefaultAccountBytecodeHash` should be validated like `L2ContractHelper.validateBytecodeHash(_l2DefaultAccountBytecodeHash)` as in `GovernanceFacet`

https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/DiamondInit.sol#L39
https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/DiamondInit.sol#L58
https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/DiamondInit.sol#L59

### No. 2
Better to have also `approveEmergencyDiamondCutAsSecurityCouncilMemberBySignature`, in case the security council members do not have access to ethereum blockchain or in case it is needed to approve just in one transaction by batch of signatures (to bypass the notice period).

### No. 3
Better to have config facet, in case some update is needed in the `config.sol`. Therefore, it is not necessary to redeploy the facets that imported config (like `Executor` and `Mailbox`)

### No. 4
`L2_LOG_BYTES` is not correct, it should be `L2_TO_L1_LOG_SERIALIZE_SIZE`
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/zksync/Config.sol#L19

### No. 5
It is not needed to have modifier `senderCanCallFunction` for the function `deposit` in both `L1ERC20Bridge` and `L1ETHBridge`, because they call the function `requestL2Transaction` in the `MailBox` that has already such modifier.
https://github.com/code-423n4/2022-10-zksync/blob/358f38736351a8a27e325dfcb665eeba5ec02bd5/ethereum/contracts/bridge/L1EthBridge.sol#L92
https://github.com/code-423n4/2022-10-zksync/blob/358f38736351a8a27e325dfcb665eeba5ec02bd5/ethereum/contracts/zksync/facets/Mailbox.sol#L112

### No. 6
For each deposit of an ERC20 token, the information of the token is read and packed and sent to L2. Even if this token is used before for deposit, again this information is sent to L2, which is waste of gas.
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L164-L169
https://github.com/code-423n4/2022-10-zksync/blob/456078b53a6d09636b84522ac8f3e8049e4e3af5/ethereum/contracts/bridge/L1ERC20Bridge.sol#L155

### No. 7
When a block is committed, its hash will be stored in `storedBlockHashes`:
https://github.com/code-423n4/2022-10-zksync/blob/358f38736351a8a27e325dfcb665eeba5ec02bd5/ethereum/contracts/zksync/facets/Executor.sol#L164
If this block is reverted, it is not removed from `storedBlockHashes`:
https://github.com/code-423n4/2022-10-zksync/blob/358f38736351a8a27e325dfcb665eeba5ec02bd5/ethereum/contracts/zksync/facets/Executor.sol#L336
The vulnerability is that in the `GettersFacet`, the function `storedBlockHash(...)` will return the hash of a reverted block if this block number is given as its parameter, while it should return 0.
https://github.com/code-423n4/2022-10-zksync/blob/358f38736351a8a27e325dfcb665eeba5ec02bd5/ethereum/contracts/zksync/facets/Getters.sol#L86