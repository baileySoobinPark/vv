---
title: Report Error API
excerpt: >
  The Originating VASP calls this API to report a transaction that has been
  permanently canceled to the VerifyVASP Central server. Through this process,
  the Beneficiary VASP can acknowledge the failure of the virtual asset
  transfer.


  ### Implementation Policy
    1. Use this API to report error situations specifically for canceled virtual asset transfers that have already undergone user verification.
    2. Call this API only if the Originating VASP has permanently canceled the virtual asset transfer.
    3. Clearly specify the reason for the cancellation in the request. Common reasons may include cases such as the Beneficiary being listed on a Sanction List.
api:
  file: 2025_05_02_TR_Enclave_API_Spec.yaml
  operationId: travelrule-enclave-report-Error
hidden: false
---