# PaymentSplitter-Solidity-smartcontract

# How to Use the Contract (Optional)  
1. **Deploy** the contract using Remix or Hardhat, setting an initial owner.  
2. Owner registers contributors using `registerContributor(address _wallet, uint256 _share)`.  
3. Send ETH to the contract using a standard transaction.  
4. Contributors call `withdraw()` to retrieve their allocated earnings.  
5. Use `pendingPayment(address _wallet)` to check how much a contributor can withdraw.  
6. Use `getContributors()` to view all registered contributors.  
