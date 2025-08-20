---
title: Owner Verification Result Report API
api:
  file: VN_Enclave_API_Spec.yaml
  operationId: verifyname-owner-verification-result-report
hidden: false
---
Called by the VASP receiving the owner verification results to report its **final transaction execution decision** to the counterparty VASP.\
The VASP that requested the owner verification must call this API to report the consolidated final result to the Central server.

## Implementation Policy

* The VASP receiving the verification results must report the final transaction execution decision.
* If the final decision is `DENIED` or `ERROR`, the reason and message fields must clearly specify the grounds for the decision.

***

## API Specification