---
title: Report Transaction Result or Error API
excerpt: >
  This API is used by the Originating VASP to report the txHash or txId of a
  blockchain transaction to the Beneficiary VASP after executing the asset
  transfer transaction on the blockchain.


  Also the Originating VASP calls this API to report a transaction that has been
  permanently canceled to the VerifyVASP Central server. Through this process,
  the Beneficiary VASP can acknowledge the failure of the virtual asset
  transfer.


  ### Implementation Policy
    1. Only transactions for which Owner Verification has been completed should be executed, and the txHash or txId must be reported via calling this API.
    2. As soon as the transaction hash is generated, this API must be called immediately to send the information to the VerifyVASP Central server.
    3. If the transaction's finality is not secured and the virtual asset transfer fails, the error must be reported immediately to the VerifyVASP Central server.
    4. Clearly specify the reason for the cancellation in the request. Common reasons may include cases such as the Beneficiary being listed on a Sanction List.
api:
  file: 2025_05_12_VN_Enclave_Spec.yaml
  operationId: verifyname-report-transaction-result-or-Error
hidden: false
---