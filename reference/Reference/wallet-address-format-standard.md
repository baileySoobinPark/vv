---
title: Wallet Address Format Standard
excerpt: >-
  Some blockchain networks have their own unique address scheme, so your VASP
  must enter a suitable address that fits the scheme supported by the network
  for each verification request.
deprecated: false
hidden: true
metadata:
  robots: index
---
This guide provides address schemes and examples for each network supported by VerifyVASP.

## Rules for Wallet Address in VerifyVASP

If the address required for verifying a user account has its own prefix, you must remove the prefix before entering it into the address field.

For example, BitcoinCash addresses have their own prefix. Remove the prefix from the BitcoinCash address before using the VerifyVASP API.

* Original BitcoinCash wallet address
  * **bitcoincash**:qq1234567890abcdefghjklmnopqrs4ty7wtp
* Removing the prefix for using VerifyVASP API
  * qq1234567890abcdefghjklmnopqrs4ty7wtp

## Address examples of each Network

The Symbol of Native Coin and the examples of wallet addresses of each blockchain network are below.

**The table could change based on VerifyVASP support. VerifyVASP recommends that operators and developers of each VASP check this table regularly.**