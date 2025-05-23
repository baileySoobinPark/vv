---
title: Transaction Report API
excerpt: >
  This API is used by the Originating VASP to report the execution of a transfer
  transaction.


  The Beneficiary VASP can receives the final result via the Callback API.


  ### Implementation Policy
    1. The Originating VASP must call this API immediately after executing the transaction and receiving the TxHash or TxId.
    2. If the Originating VASP fails to call this API after executing the transaction, the Beneficiary VASP will call the Check Transaction Status API to confirm the current status of the transaction.
api:
  file: VN_Enclave_API_Spec.yaml
  operationId: verifyname-transaction-report
hidden: false
---