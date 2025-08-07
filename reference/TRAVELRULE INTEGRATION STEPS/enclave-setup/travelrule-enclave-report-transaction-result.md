---
title: Report Transaction Result API
api:
  file: TR_Enclave_API_Spec.yaml
  operationId: travelrule-enclave-report-Transaction-Result
hidden: false
---
This API is used by the sending VASP to report the transaction hash (TxHash or TxId) to the receiving VASP after executing an on-chain transaction for asset transfer.

***

## Implementation Policy

1. Transactions should only be executed after successful user verification.
2. Upon execution, the transaction hash (TxHash or TxId) must be reported via this API.
3. This API should be called immediately after the transaction hash is generated to notify the VerifyVASP Central Server.
4. If the asset transfer fails due to lack of finality, the sending VASP must call the Report Error API immediately to report the failure to the Central Server.

***

## API Specification