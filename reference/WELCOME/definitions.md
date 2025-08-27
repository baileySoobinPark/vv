---
title: Definitions
deprecated: false
hidden: false
metadata:
  robots: index
---
## VASP

* Virtual Asset Service Provider
* e.g., cryptocurrency exchanges or custody service providers.

## Verification

* A verification process performed before sending virtual assets to satisfy the Travel Rule
* Generally, verification is a process where the originator information is sent to the beneficiary VASP, the beneficiary VASP verifies the beneficiary information entered by the originator and delivers the results.

## Originator

* A person who wishes to send virtual assets
* A person who requested withdrawal from VASP

## Beneficiary

* A person who receives virtual assets

## Ordering VASP

* The VASP to which an originator belongs
* aka. Originating VASP
* In the FATF standards, the term is referred to as **Ordering VASP**, whereas the IVMS101 standard uses the term **Originating VASP**.

## Beneficiary VASP

* The VASP to which a beneficiary belongs

## VV Central Server

* A communication mediating server operated by VerifyVASP
* Every verification request performed through VerifyVASP is mediated to each VASP via VV Central Server.

## Enclave, Enclave Server

* A server module to be installed in each VASP infrastructure
* A backend server module to ease VV connection. Provided by VerifyVASP.
* Handles detailed communications with the VV Central Server on behalf of the VASP

## Sanction Screening

* A process of pre-verification to check whether a certain user or wallet address is on the sanction list.

## Risk Assessment

* It includes both pre-validation and post-validation, and uses information such as blockchain addresses, transactions, individuals, and entities for sending and receiving to assess the risk of a given transaction.
* We typically use solutions from 3rd party vendors who have a lot of transaction data and fraud history data to assess risk.

## Alliance

* VASPs registered in VerifyVASP

## TxHash

* ID value which can identify the transaction in the blockchain after the virtual asset was performed in the actual blockchain

## Vout

* In cases where multiple transfers can be included in a single transaction, such as Bitcoin, an index value indicating which transfer corresponds to within a single transaction
