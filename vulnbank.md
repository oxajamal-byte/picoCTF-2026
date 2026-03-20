# VulnBank — Reentrancy Attack

**Category:** Blockchain

## Overview

A smart contract that acts as a simple bank. You can deposit and withdraw ETH. The vulnerability is a classic reentrancy bug where the contract sends you ETH before updating your balance.

## How reentrancy works

When VulnBank sends ETH to your address, if your address is a contract then your fallback function gets called automatically. Inside that fallback you call `withdraw` again. Since VulnBank hasnt updated your balance yet it still thinks you have funds and sends more ETH. This keeps looping until the bank is empty.

This is the exact bug that caused the DAO hack in 2016. It was such a big deal that Ethereum literally forked into two chains over it.

## Approach

Deployed an attacker contract that:

1. Deposits a small amount into VulnBank
2. Calls withdraw
3. In the receive/fallback function checks if the bank still has balance and calls withdraw again

Drained VulnBank to zero and the flag appeared.

The fix is straightforward. Update state before making external calls. Check conditions first, update balances second, transfer funds last. If VulnBank set the balance to zero before sending ETH the reentrant call would see a zero balance and stop.

## What made this hard

The competition environment had basically zero disk space. Couldnt install web3.py or any Ethereum libraries. So I had to do everything with raw JSON-RPC calls through Python's built-in urllib. Downloaded a static solc binary to `/tmp` for compiling. Computed function selectors by hand. Signed and sent transactions as raw HTTP requests:

```python
import urllib.request, json

payload = {
    "jsonrpc": "2.0",
    "method": "eth_sendRawTransaction",
    "params": [signed_tx],
    "id": 1
}

req = urllib.request.Request(
    rpc_url,
    json.dumps(payload).encode(),
    {"Content-Type": "application/json"}
)
response = urllib.request.urlopen(req)
```

Honestly this was more painful than the actual exploit but it forced me to understand what web3 libraries are doing under the hood. Every call they make is just JSON-RPC. Knowing that saved me on the other blockchain challenges too.
