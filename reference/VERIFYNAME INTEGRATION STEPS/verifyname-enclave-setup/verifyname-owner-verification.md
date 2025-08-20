---
title: Request Owner Verification API
api:
  file: VN_Enclave_API_Spec.yaml
  operationId: verifyname-owner-verification
hidden: false
---
Compares the ownership information of a specified wallet address with the user information held by your VASP to verify whether the originator and beneficiary are the same person.

***

Owner verification can be performed in two scenarios:

* **Post Verification**: After the ordering VASP executes the withdrawal transaction, upon request from the beneficiary VASP, verify whether the asset transfer requester is the same person as the beneficiary.
* **Pre Verification**: Before executing the withdrawal transaction, upon request from the ordering VASP, verify whether the originator and the owner of the beneficiary wallet address are the same person.

In the API response, each verification item in the `verification_results` object returns one of:`MATCHED`, `MISMATCHED`, or `SKIPPED`.

***

## API 명세