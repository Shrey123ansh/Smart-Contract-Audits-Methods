# Audits and Security in Smart Contracts

## Overview

This repository focuses on ensuring the security and reliability of smart contracts through a comprehensive approach to development, testing, and auditing. By following the best practices, conducting various types of tests, and performing audits, we aim to create robust and secure smart contracts.

## Smart Contract Development

   - Testing
   - Gas & Memory Optimizations (Don't make useless optimizations)
   - Code coverage (>90%)
   - Documentation
   - Peer review
   - Static analysis (optional)
   - Symbolic execution (optional)
   - Audit

## Different kinds of tests

   - Unit tests
   - local blockchain, one deployed contract, the rest mocks
   - Integration tests
   - local blockchain, multiple deployed contracts, no mocks
   - End to End tests
   - mainnet fork, the same as Integration
   - Property-based tests
   - known as "fuzz tests"

#### Common Audits

1. **Re-Entrancy**
   - Update storage variable, THEN perform transfer.

   - Use OpenZeppelin's ReentrancyGuard or implement your own re-entrancy modifier.

   ```solidity
   function withdraw(uint256 amount) public nonReentrant {
       require(amount <= balances[msg.sender]);
       balances[msg.sender] -= amount;
       (bool sent,) = msg.sender.call{value: amount}("");
       require(sent, "Failed to send Ether");
   }
   ```

2. **On-chain Randomness**
   - Solution: Avoid using blockhash or block.timestamp for randomness; use Chainlink VRF.

3. **Overflow/Underflow**
   - Solution: Use SafeMath library for Solidity versions <0.8; from 0.8 onward, SafeMath is integrated natively.

4. **Price Oracle Attacks**
   - Solution: Avoid low liquidity tokens; be cautious with TWAP Oracles; use multiple data sources or Chainlink Price Feeds.
   - Don't use a single oracle or single data source (DEX is still a centralized price oracle)

#### Best Practices

1. **Monitor & Upgrade Dependencies**
   - Monitor & upgrade package.json dependencies
   - Use stable Solidity versions
   - 0.5.16 0.5.17
   - 0.6.11 0.6.12
   - 0.7.5-0.7.6
   - 0.8.4
   - Avoid floating pragma


2. **Checks-Effects-Interactions Pattern**
   - Use the Checks-Effects-Interactions Pattern
   - Checks (require(s), modifier(s), )
   - Update storage variables
   - Call another contract(s)
   - Take Warnings Seriously
   - Keep it Small and Modular
   - KISS Keep it as simple as possible

3. **Protect Withdrawal Functions**
   - Always protect withdrawal functions (SWC-105)
   - Avoid selfdestruct
   - Modifiers shouldn't update state
   - If you are using solc version <0.8 use SafeMath
   - Use Reentrancy Guard

4. **Send ETH via Call**
   - Send ETH via call, avoid transfer and send
   ```solidity
  (bool sent, bytes memory data)to.call(value: msg.value)(""); require(sent, "Failed to send Ether");
   ```
   - Avoid pseudo-randomness (SWC-120)
   -Use Chainlink VRF
   -Avoid tx.origin, use msg.sender


5. **Divide Before Multiply**
   - Divide before multiplying to avoid precision errors.
   - return result basePercent / 10000
   - Use increaseAllowance & decreaseAllowance instead of approve.
   - Check for address (0x0)

6. **Test External Integrations**

   - Always test the extreme case of external integrations • Can Uniswap run out of liquidity? Test that scenario
   - Mainnet forking Fork & Test on the real environment
   - Don't make useless optimizations, security is top priority


7. **CI/CD (Continuous Integration/Continuous Deployment)**
   - CI/CD (block merging PRs)
   - Run test suite on each PR/push & mandate that all PASS
   - Mandate >90% code coverage
   - Mandate that the code is properly
   - formatted using linter


### Audit Types

1. **New Audit**
   - Conduct a thorough audit for a new project.

2. **Repeat Audit**
   - Audit familiar projects with new features.

3. **Fix Audit**
   - Review fixes for bugs identified in prior audits.

4. **Retainer Audit**
   - Constantly review project updates to maintain security.

5. **Incident Audit**
   - Conduct an exploit review in response to incidents.



