---
title: Check Transaction Status API
api:
  file: TR_Enclave_API_Spec.yaml
  operationId: travelrule-enclave-check-Transaction-Status
hidden: false
---
This API allows the beneficiary VASP to query the originating VASP for the status of a transaction. It is typically used when the beneficiary VASP has completed user verification but has not received a Transaction Report or Error Report from the originating VASP within a certain timeframe.

<br />

## API Use Case

This API is not only used to check the status of previously verified transactions but also for tracking unrecognized incoming transactions, as illustrated below:

1. When the beneficiary VASP detects an unrecognized deposit, it first calls the Lookup Verification Result API to search for any verification history related to the specific transaction hash.
2. If no verification history is found, the beneficiary VASP waits for a period of time for the originating VASP to call the Transaction Report API.
3. If no report is received after waiting, the beneficiary VASP calls the Lookup Verification Result API again to retrieve a list of potential verification candidates.
4. To narrow the candidate list, the beneficiary VASP uses filters such as the deposit address (beneficiaryAccountNumber), asset amount, and asset symbol detected in the transaction.
5. For each candidate, the beneficiary VASP calls the Check Transaction Status API to determine whether it matches the actual transaction hash.

<br />

***

## API Specification