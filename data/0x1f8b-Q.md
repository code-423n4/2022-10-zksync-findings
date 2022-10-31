- [Low](#low)
    - [**1. Outdated packages**](#1-outdated-packages)
    - [**2. Integer overflow by unsafe casting**](#2-integer-overflow-by-unsafe-casting)
    - [**3. Lack of checks supportsInterface**](#3-lack-of-checks-supportsinterface)
- [Non critical](#non-critical)
    - [**4. Avoid hardcoded values**](#4-avoid-hardcoded-values)
    - [**5. Open TODO**](#5-open-todo)
    - [**6. Use abstract for base contracts**](#6-use-abstract-for-base-contracts)

# Low

## **1. Outdated packages**

Some used packages are out of date, it is good practice to use the latest version of these packages:

```
"@openzeppelin/contracts": "4.6.0",
"@openzeppelin/contracts-upgradeable": "4.6.0",
```

**Reference:**

- https://github.com/OpenZeppelin/openzeppelin-contracts/releases

**Affected source code:**

- [package.json:9-10](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/package.json#L9-L10)
- [package.json:9](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/package.json#L9)

## **2. Integer overflow by unsafe casting**

Keep in mind that the version of solidity used, despite being greater than `0.8`, does not prevent integer overflows during casting, it only does so in mathematical operations.

It is necessary to safely convert between the different numeric types.

**Recommendation:**

Use a [safeCast](https://docs.openzeppelin.com/contracts/3.x/api/utils#SafeCast) from Open Zeppelin.

```javascript
        checkpoint.votes = uint192(_newTotalVotes);
```

**Affected source code:**

- [Mailbox.sol:125](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L125)
- [Diamond.sol:190](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L190)
- [Diamond.sol:207](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L207)
- [Diamond.sol:239](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L239)
- [Diamond.sol:268](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L268)

## **3. Lack of checks `supportsInterface`**

The `EIP-165` standard helps detect that a smart contract implements the expected logic, prevents human error when configuring smart contract bindings, so it is recommended to check that the received argument is a contract and supports the expected interface.

**Reference:**

- https://eips.ethereum.org/EIPS/eip-165

**Affected source code:**

- [L2ERC20Bridge.sol:36](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ERC20Bridge.sol#L36)
- [L1ERC20Bridge.sol:59](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1ERC20Bridge.sol#L59)
- [L1ERC20Bridge.sol:79](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1ERC20Bridge.sol#L79)
- [L2ETHBridge.sol:31](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ETHBridge.sol#L31)
- [L1EthBridge.sol:49-50](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1EthBridge.sol#L49-L50)
- [Governance.sol:97](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Governance.sol#L97)
- [DiamondInit.sol:55](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/DiamondInit.sol#L55)

---

# Non critical

## **4. Avoid hardcoded values**

It is not good practice to hardcode values, but if you are dealing with addresses much less, these can change between implementations, networks or projects, so it is convenient to remove these values from the source code.

**Affected source code:**

- [Config.sol:5](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Config.sol#L5)
- [Config.sol:20](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Config.sol#L20)
- [Diamond.sol:10-11](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L10-L11)
- [Diamond.sol:14](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Diamond.sol#L14)
- [ReentrancyGuard.sol:27](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/common/ReentrancyGuard.sol#L27)

## **5. Open TODO**

The code that contains "open todos" reflects that the development is not finished and that the code can change a posteriori, prior release, with or without audit.

**Affected source code:**

> // TODO: estimVerifier.sol#L132ate gas for L1 execute

- [Mailbox.sol:94](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L94)

> // TODO: Restore after stable priority op fee modeling. (SMA-1230)

- [Mailbox.sol:127](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L127)

> // TODO: Restore after fee modeling will be stable. (SMA-1230)

- [Mailbox.sol:169](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L169)

> // TODO: The verifier integration is not finished yet, change the structure for compatibility later

- [IExecutor.sol:56](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IExecutor.sol#L56)

> // TODO: 

- [PairingsBn254.sol:208](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/PairingsBn254.sol#L208)

> // TODO: change constant to the real root hash of empty Merkle tree (SMA-184)

- [Config.sol:28](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Config.sol#L28)

> // require(serialized_proof.length == 44); TODO

- [Verifier.sol:132](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Verifier.sol#L132)

> // TODO: not use array while there is only D_next

- [Plonk4VerifierWithAccessToDNext.sol:43](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol#L43)

> // TODO: 

- [Plonk4VerifierWithAccessToDNext.sol:224](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol#L224)

> // require(vk.num_inputs > 0); // TODO

- [Plonk4VerifierWithAccessToDNext.sol:304](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol#L304)

> // TODO we may use batched lagrange compputation

- [Plonk4VerifierWithAccessToDNext.sol:308](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol#L308)

> // TODO add one into non-residues during codegen?

- [Plonk4VerifierWithAccessToDNext.sol:454](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol#L454)

> // TODO

- [Plonk4VerifierWithAccessToDNext.sol:485](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol#L485)

## **6. Use `abstract` for base contracts**

`abstract` contracts are contracts that have at least one function without its implementation. **An instance of an abstract cannot be created.**

**Reference:**

- https://docs.soliditylang.org/en/v0.6.2/contracts.html#abstract-contracts

**Affected source code:**

- [Base.sol:11](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Base.sol#L11)
- [Getters.sol:13](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Getters.sol#L13)

