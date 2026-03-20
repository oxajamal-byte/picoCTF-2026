# IntOverflowBank

**Category:** Blockchain

## Overview

A bank contract that uses unsigned integers for balances without checking for overflow.

## Approach

The max value of a uint256 is 2^256 - 1 which is a number with 78 digits. The contract doesnt validate arithmetic on deposits.

I deposited 1 wei first to establish an account. Then deposited the max uint256 value. The addition wraps around past the maximum and my balance became astronomically large. Withdrew what I needed and the flag appeared.

This was written with an older Solidity compiler on purpose. Starting with Solidity 0.8.0 the compiler adds automatic overflow checks and the transaction would just revert instead of wrapping. Before that, everyone used OpenZeppelin's SafeMath library to add those checks manually. You can still bypass the 0.8.0 protections with `unchecked` blocks but you have to do it deliberately which is the whole point.

Quick one but a good reminder to always check what compiler version a contract was built with when auditing.
