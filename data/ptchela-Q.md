https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/.solhint.json

Better to use "solhint:recommended" than "solhint:default" rulesets for the “extends” property in the configuration file. Also I would recommend to add there "mark-callable-contracts" -Explicitly mark all external contracts as trusted or untrusted. For more security. 
At least two lines should be added:

    {
        "extends": "solhint:recommended",
        "plugins": ["prettier"],
        "rules": {
          "prettier/prettier": "error",
          "no-inline-assembly": false
          "avoid-suicide": "error",
          "avoid-sha3": "warn"
        }
      }

