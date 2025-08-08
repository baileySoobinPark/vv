---
title: Owner Verification Transaction Report API
api:
  file: VN_Enclave_API_Spec.yaml
  operationId: verifyname-transaction-report
hidden: false
---
Used by the sending VASP to report the `TxHash` or `TxId` of the executed blockchain asset transfer transaction to the receiving VASP.\
After owner verification, if the final decision is reported as `VERIFIED` via the Result Report API, the asset transfer transaction should be executed and reported to the Central server through this API.

<br />

<br />

## Implementation Policy

* Only transactions with a final decision of `VERIFIED` should be executed after owner verification.
* Once the transaction is executed and the TxHash is generated, this API must be called **immediately** to send the information to the VerifyVASP Central server.
* If the transaction fails or is canceled, resulting in the termination of the asset transfer, the **Result Report API** must be called immediately to **report the termination to the Central server**.

<br />

## API Specification