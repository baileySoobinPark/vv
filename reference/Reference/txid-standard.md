---
title: TXID Standard
excerpt: >-
  This guide provides the standards and examples for Transaction IDs to
  effectively support data transportation between VASPs.
deprecated: false
hidden: true
metadata:
  robots: index
---
## Rules for Transaction IDs in VerifyVASP

Transaction ID or Transaction Hash must be used as the original output data returned by the blockchain node. Never add a prefix such as “0x” to the original output data manually.

For example, the Transaction Hash of the Monero network doesn’t have a prefix. If you add a prefix (“0x”) to the Transaction Hash and submit a verification request, a “Transaction Not Found” error will be returned.

* **Transaction Hash of Monero network**
  * `6c3530d87e60b110801e5dbf48995e7c5d510ef653e5752a52902ae6df8a01e6`
* **The result of requesting a verification with the prefix(“0x”)**
  * ```
    Transaction Not Found
    0x6c3530d87e60b110801e5dbf48995e7c5d510ef653e5752a52902ae6df8a01e6
    ```

<br />

## Examples of Transaction Hash

The table below provides the network name, symbol, and examples of transaction hashes that are the original output data from the node.