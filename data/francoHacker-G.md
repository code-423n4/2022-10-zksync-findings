[G-01] x = x + y is cheaper than x += y;

    s.diamondCutStorage.currentProposalId += 1;

fix: 
   
    s.diamondCutStorage.currentProposalId = s.diamondCutStorage.currentProposalId  + 1;


instances:

https://github.com/code-423n4/2022-10-zksync/blob/main/ethereum/contracts/zksync/facets/DiamondCut.sol#L29



[G-02] USE NAMED RETURNS FOR LOCAL VARIABLES WHERE IT IS POSSIBLE 

      function decimals() public view override returns (uint8) {
        // If method is not available, behave like a token that does not implement this method - revert on call.
        if (availableGetters.ignoreDecimals) revert();
        return decimals_;
      }

FIX

      function decimals() public view override returns (uint8 decimals_) {
        // If method is not available, behave like a token that does not implement this method - revert on call.
        if (availableGetters.ignoreDecimals) revert();
       
      }

instances:

https://github.com/code-423n4/2022-10-zksync/blob/main/zksync/contracts/bridge/L2StandardERC20.sol#L126