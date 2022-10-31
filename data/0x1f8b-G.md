- [Disclosure note:](#disclosure-note)
- [Gas Report](#gas-report)
    - [**1. Use require instead of assert**](#1-use-require-instead-of-assert)
    - [**2. Avoid compound assignment operator in state variables**](#2-avoid-compound-assignment-operator-in-state-variables)
        - [Total gas saved: **13 * 1 = 13**](#total-gas-saved-13--1--13)
    - [**3. Use calldata instead of memory**](#3-use-calldata-instead-of-memory)
    - [**4. Shift right or left instead of dividing or multiply by 2**](#4-shift-right-or-left-instead-of-dividing-or-multiply-by-2)
        - [Total gas saved: **172 * 1 = 172**](#total-gas-saved-172--1--172)
    - [**5. There's no need to set default values for variables**](#5-theres-no-need-to-set-default-values-for-variables)
        - [Total gas saved: **8 * 2 = 16**](#total-gas-saved-8--2--16)
    - [**6. Unnecessary cast in Mailbox.serializeL2Transaction**](#6-unnecessary-cast-in-mailboxserializel2transaction)
    - [**7. Gas saving using immutable**](#7-gas-saving-using-immutable)
    - [**8. Reorder structure layout**](#8-reorder-structure-layout)
    - [**9. Use require instead of assert**](#9-use-require-instead-of-assert)

# Disclosure note:

*First of all, please note that there are "known issues" that were not referenced in the [report](https://gist.github.com/Picodes/1f87a82e954cc749dea9d9961d5f4dff), these lines were included in this report because were not present in the public one.*

# Gas Report

## **1. Use `require` instead of `assert`**

The `assert()` and `require()` functions are a part of the error handling aspect in Solidity. Solidity makes use of state-reverting error handling exceptions. This means all changes made to the contract on that call or any sub-calls are undone if an error is thrown. It also flags an error.

They are quite similar as both check for conditions and if they are not met, would throw an error.

The big difference between the two is that the `assert()` function when false, **uses up all the remaining gas and reverts all the changes made.**

Meanwhile, a `require()` function when false, also reverts back all the changes made to the contract but **does refund all the remaining gas fees we offered to pay**. This is the most common Solidity function used by developers for debugging and error handling.

**Affected source code:**

- [DiamondCut.sol:16](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/DiamondCut.sol#L16)

## **2. Avoid compound assignment operator in state variables**

Using compound assignment operators for state variables (like `State += X` or `State -= X` ...) it's more expensive than using operator assignment (like `State = State + X` or `State = State - X` ...).

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
uint private _a;
function testShort() public {
_a += 1;
}
}

contract TesterB {
uint private _a;
function testLong() public {
_a = _a + 1;
}
}
```

Gas saving executing: **13 per entry**

```
TesterA.testShort: 43507
TesterB.testLong:  43494
```

**Affected source code:**

- [DiamondCut.sol:29](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/DiamondCut.sol#L29)

### Total gas saved: **13 * 1 = 13**

## **3. Use `calldata` instead of `memory`**

Some methods are declared as `external` but the arguments are defined as `memory` instead of as `calldata`.

By marking the function as `external` it is possible to use `calldata` in the arguments shown below and save significant gas.

**Recommended change:**

```diff
-   function decodeString(bytes memory _input) external pure returns (string memory result) {
+   function decodeString(bytes calldata _input) external pure returns (string memory result) {
        (result) = abi.decode(_input, (string));
    }
```

**Affected source code:**

- [ExternalDecoder.sol:10-12](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/ExternalDecoder.sol#L10-L12)
- [ExternalDecoder.sol:15-17](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/ExternalDecoder.sol#L15-L17)

## **4. Shift right or left instead of dividing or multiply by 2**

Shifting one to the right will calculate a division by two.

he `SHR` opcode only requires 3 gas, compared to the `DIV` opcode's consumption of 5. Additionally, shifting is used to get around Solidity's division operation's division-by-0 prohibition.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.16;

contract TesterA {
function testDiv(uint a) public returns (uint) { return a / 2; }
}

contract TesterB {
function testShift(uint a) public returns (uint) { return a >> 1; }
}
```

Gas saving executing: **172 per entry**

```
TesterA.testDiv:    21965 
TesterB.testShift:  21793   
```

The same optimization can be used to multiply by 2, using the left shift.

```javascript
pragma solidity 0.8.16;

contract TesterA {
function testMul(uint a) public returns (uint) { return a * 2; }
}

contract TesterB {
function testShift(uint a) public returns (uint) { return a << 1; }
}
```

Gas saving executing: **201 per entry**

```
TesterA.testMul:    21994
TesterB.testShift:  21793    
```

**Affected source code:**

`/`:
- [Merkle.sol:34](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/libraries/Merkle.sol#L34)

### Total gas saved: **(172 * 1) = 172**

## **5. There's no need to set default values for variables**

If a variable is not set/initialized, the default value is assumed (0, `false`, 0x0 ... depending on the data type). You are simply wasting gas if you directly initialize it with its default value.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
function testInit() public view returns (uint) { uint a = 0; return a; }
}

contract TesterB {
function testNoInit() public view returns (uint) { uint a; return a; }
}
```

Gas saving executing: **8 per entry**

```
TesterA.testInit:   21392
TesterB.testNoInit: 21384
```

**Affected source code:**

- [L1EthBridge.sol:33](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/bridge/L1EthBridge.sol#L33)
- [L2ETHBridge.sol:28](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ETHBridge.sol#L28)

### Total gas saved: **8 * 2 = 16**

## **6. Unnecessary cast in `Mailbox.serializeL2Transaction`**

It's possible to remove the following casts:

```diff
    function serializeL2Transaction(
        uint256 _txId,
        uint256 _l2Value,
        address _sender,
        address _contractAddressL2,
        bytes calldata _calldata,
        uint256 _ergsLimit,
        bytes[] calldata _factoryDeps
    ) public pure returns (L2CanonicalTransaction memory) {
        return
            L2CanonicalTransaction({
                txType: PRIORITY_OPERATION_L2_TX_TYPE,
                from: uint256(uint160(_sender)),
                to: uint256(uint160(_contractAddressL2)),
                ergsLimit: _ergsLimit,
                ergsPerPubdataByteLimit: uint256(1),
                maxFeePerErg: uint256(0),
                maxPriorityFeePerErg: uint256(0),
                paymaster: uint256(0),
-               reserved: [uint256(_txId), _l2Value, 0, 0, 0, 0],
+               reserved: [_txId, _l2Value, 0, 0, 0, 0],
                data: _calldata,
                signature: new bytes(0),
                factoryDeps: _hashFactoryDeps(_factoryDeps),
                paymasterInput: new bytes(0),
                reservedDynamic: new bytes(0)
            });
    }
```

**Affected source code:**

- [Mailbox.sol:206](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/Mailbox.sol#L206)

## **7. Gas saving using `immutable`**

It's possible to avoid storage access a save gas using `immutable` keyword for the following variables:

It's also better to remove the initial values, because they will be set during the constructor.

**Affected source code:**

- [L2ERC20Bridge.sol:19](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ERC20Bridge.sol#L19)
- [L2ERC20Bridge.sol:23](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ERC20Bridge.sol#L23)
- [L2ERC20Bridge.sol:26](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ERC20Bridge.sol#L26)
- [L2ETHBridge.sol:22](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/zksync/contracts/bridge/L2ETHBridge.sol#L22)

## **8. Reorder structure layout**

The following structs could be optimized moving the position of certains values in order to save slot storages:

`StoredBlockInfo` in [IExecutor.sol#L15-L24](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/interfaces/IExecutor.sol#L15-L24)

```diff
    struct StoredBlockInfo {
        uint64 blockNumber;
+       uint64 indexRepeatedStorageChanges;
        bytes32 blockHash;
-       uint64 indexRepeatedStorageChanges;
        uint256 numberOfLayer1Txs;
        bytes32 priorityOperationsHash;
        bytes32 l2LogsTreeRoot;
        uint256 timestamp;
        bytes32 commitment;
    }
```

`AppStorage` in [Storage.sol:69-106](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/Storage.sol#L69-L106)

```diff
struct AppStorage {
    /// @dev Storage of variables needed for diamond cut facet
    DiamondCutStorage diamondCutStorage;
    /// @notice Address which will exercise governance over the network i.e. change validator set, conduct upgrades
    address governor;
+   bool zkPorterIsAvailable;
    /// @notice Address that governor proposed as one that will replace it
    address pendingGovernor;
    /// @notice List of permitted validators
    mapping(address => bool) validators;
    /// @dev Verifier contract. Used to verify aggregated proof for blocks
    Verifier verifier;
    /// @notice Total number of executed blocks i.e. blocks[totalBlocksExecuted] points at the latest executed block (block 0 is genesis)
    uint256 totalBlocksExecuted;
    /// @notice Total number of proved blocks i.e. blocks[totalBlocksProved] points at the latest proved block
    uint256 totalBlocksVerified;
    /// @notice Total number of committed blocks i.e. blocks[totalBlocksCommitted] points at the latest committed block
    uint256 totalBlocksCommitted;
    /// @dev Stored hashed StoredBlock for block number
    mapping(uint256 => bytes32) storedBlockHashes;
    /// @dev Stored root hashes of L2 -> L1 logs
    mapping(uint256 => bytes32) l2LogsRootHashes;
    /// @dev Container that stores transactions requested from L1
    PriorityQueue.Queue priorityQueue;
    /// @dev The smart contract that manages the list with permission to call contract functions
    IAllowList allowList;
    /// @notice Part of the configuration parameters of ZKP circuits. Used as an input for the verifier smart contract
    VerifierParams verifierParams;
    /// @notice Bytecode hash of bootloader program.
    /// @dev Used as an input to zkp-circuit.
    bytes32 l2BootloaderBytecodeHash;
    /// @notice Bytecode hash of default account (bytecode for EOA).
    /// @dev Used as an input to zkp-circuit.
    bytes32 l2DefaultAccountBytecodeHash;
    /// @dev Indicates that the porter may be touched on L2 transactions.
    /// @dev Used as an input to zkp-circuit.
-   bool zkPorterIsAvailable;
}
```

## **9. Use `require` instead of `assert`**

The `assert()` and `require()` functions are a part of the error handling aspect in Solidity. Solidity makes use of state-reverting error handling exceptions. This means all changes made to the contract on that call or any sub-calls are undone if an error is thrown. It also flags an error.

They are quite similar as both check for conditions and if they are not met, would throw an error.

The big difference between the two is that the `assert()` function when false, **uses up all the remaining gas and reverts all the changes made.**

Meanwhile, a `require()` function when false, also reverts back all the changes made to the contract but **does refund all the remaining gas fees we offered to pay**. This is the most common Solidity function used by developers for debugging and error handling.

**Affected source code:**

- [DiamondCut.sol:16](https://github.com/code-423n4/2022-10-zksync/blob/4db6c596931a291b17a4e0e2929adf810a4a0eed/ethereum/contracts/zksync/facets/DiamondCut.sol#L16)
