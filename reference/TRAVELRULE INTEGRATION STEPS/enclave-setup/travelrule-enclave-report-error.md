---
title: Report Error API
api:
  file: TR_Enclave_API_Spec.yaml
  operationId: travelrule-enclave-report-Error
hidden: false
---
This API is used by the sending VASP to notify the receiving VASP when the asset transfer is aborted due to a verification failure or internal processing error.\
By calling this API, the receiving VASP can acknowledge the termination of the transaction and stop related monitoring or inform the customer accordingly.

## Implementation Policy

1. This API must only be used after user verification is completed, and the sending VASP decides to abort the transfer.
2. It should only be called when the asset transfer is permanently cancelled by the sending VASP.
3. The reason for cancellation must be clearly stated in the API request.

***

## API Specification