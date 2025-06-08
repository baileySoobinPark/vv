---
title: Check Transaction Status API
excerpt: >
  This API is called by the Beneficiary VASP when it cannot confirm the results
  of the Report Transaction Result or Report Error API for a verified user
  within a certain timeframe.


  **Implementation Guide**
    1. When the Beneficiary VASP detects an incoming deposit, it should first call the Verification Result Lookup API with the txHash filter to check for any matching verification records.
    2. If no matching txHash is found, the Beneficiary VASP can wait for a certain period for the Originating VASP to call the Transaction Report API.
    3. After this waiting period, if no transaction report has been received from the Originating VASP, the Beneficiary VASP can use the Verification Result Lookup API to identify potential candidate verifications.
    4. To narrow down the candidates, search using the deposit’s detected from address and to address as the originatorAccountNumber and beneficiaryAccountNumber, respectively.
    5. For the filtered candidate verifications, the Beneficiary VASP can then call the Check Transaction Status API for each to match the transaction ID information.
api:
  file: TR_Enclave_API_KR_Spec.yaml
  operationId: travelrule-enclave-check-Transaction-Status
hidden: false
---