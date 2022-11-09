
This QA regards the following file **_hardhat.config.ts_**, found in this path:
2022-10-zksync/ethereum/hardhat.config.ts

Hardhat.config.ts states in line 22- 23:
```
  solidity: {
        version: '0.8.17’, 
 ``` 

Here we see that the whole projects codebase is complied by solc version 0.8.17. This is the latest version of solidity. While it benefits the project at this stage to use this version for testing and debugging. The poupular argument of the newest version having bugs, has affected previous version as well.

Check out this thread for further explanation: 

[ethereum.stackexchange](https://ethereum.stackexchange.com/questions/94361/how-do-i-determine-the-most-recommended-safest-to-use-version-of-solidity) (last checked 09.11.2022)


Since this project uses the framework Hardhat, which at this date supports solidity 0.8.x up to 0.8.16, using the unsupported version may lead to «… Solidity stack stop working are incorrect or incomplete. It could also mean that console.log stops working.»

[hardhat](https://hardhat.org/hardhat-runner/docs/reference/solidity-support)  (last checked 09.11.2022) 

For these stated reasons I recommend that the Zksync dev team use **_solc version 0.8.16_** in hardhat.config.ts