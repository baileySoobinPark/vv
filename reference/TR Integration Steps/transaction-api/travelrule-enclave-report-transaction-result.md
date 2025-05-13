---
title: Report Transaction Result API
excerpt: >
  This API is used by the Originating VASP to report the txHash or txId of a
  blockchain transaction to the Beneficiary VASP after executing the asset
  transfer transaction on the blockchain.

  ### Implementation Policy
    1. Only transactions for which User Verification has been completed should be executed, and the txHash or txId must be reported via calling this API.
    2. As soon as the transaction hash is generated, this API must be called immediately to send the information to the VerifyVASP Central server.
    3. If the transaction's finality is not secured and the virtual asset transfer fails, the Report Error API must be executed immediately to report the error to the VerifyVASP Central server.
api:
  file: 2025_05_13_TR_Enclave_API_Spec.yaml
  operationId: travelrule-enclave-report-Transaction-Result
hidden: false
---