---
title: Owner Verification Result Report API
excerpt: >
  This API is used by the VASP that received the Owner Verification result to
  report the final verification decision.


  The VASP that performed the Owner Verification receives the final result via
  the Callback API.


  ### Implementation Policy
    1. The VASP that receives the verification result must report the final verification result.
    2. If the final verification result is `DENIED` or `ERROR`, you must provide an appropriate reason in both the `reason` and `message` fields.
api:
  file: VN_Enclave_API_Spec.yaml
  operationId: verifyname-owner-verification-result-report
hidden: false
---