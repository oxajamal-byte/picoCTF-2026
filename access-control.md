# AccessControl

**Category:** Blockchain

## Overview

A smart contract with a `changeOwner` function that has zero access restrictions. Anyone can call it.

## Approach

Read the contract. `changeOwner` is public with no modifier checking who the caller is. Called it with my address, became the owner, called `solve()`, got the flag.

There was no real exploit here. The function just shouldnt have been public. In Solidity you use an `onlyOwner` modifier or OpenZeppelin's `Ownable` contract to restrict sensitive functions. Missing access controls on smart contracts have led to millions in losses in real DeFi protocols.
