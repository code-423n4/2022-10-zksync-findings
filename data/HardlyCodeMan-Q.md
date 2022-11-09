## Low Findings Report 2022-10-zksync

### Unspecific Compiler Version Pragma - pragma ^
A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain. 

Of the files in scope, there are 39 occurrances in 39 files of pragma solidity ^0.8.0; or pragma solidity ^0.8;

Excluding ethereum/cache/solpp-generated-contracts/dev-contracts/ and out of scope items listed in the audit specification:
- ethereum/contracts/zksync/Verifier.sol
- ethereum/contracts/zksync/libraries/PairingsBn254.sol	
- ethereum/contracts/zksync/libraries/TranscriptLib.sol
- ethereum/contracts/zksync/Plonk4VerifierWithAccessToDNext.sol
- ethereum/contracts/dev-contracts/*
