---
title: Check Transaction Status API
excerpt: >
  This API must be implemented by your VASP to fulfill its role as a Originating
  VASP. Its primary purpose is to retrieve and return the current status of a
  transfer transaction. 

  This API is typically called by the Beneficiary VASP when asynchronous status
  updates from the Originating VASP are delayed or missing.

  The Enclave will invoke this API upon receiving transaction status checking
  requests from the Beneficiary VASP.


  ## Functional Requirements
    **1. Mapping Request ID to the Corresponding Transaction Hash**
    - Your VASP, acting as the Originating VASP, must map the `request_id` (received in response to a previous 'Verify Name API' request) to the transaction hash.

    **2. Retrieving and Responding On-Chain Transaction Status**
    - Using the transaction hash, retrieve the current status of the on-chain transaction. The transaction status must be returned in the transaction_status field, which supports the following allowed values:
      - **`PENDING`**: The transaction is awaiting submission to the blockchain for any reason.
      - **`PROCESSING`**: The transaction has been submitted to the blockchain and is waiting to be mined.
      - **`WAIT-CONFIRM`**: The transaction has been mined, but finality has not yet been achieved.
      - **`CONFIRMED`**: The transaction has been mined and has achieved finality.
      - **`CANCELED`**: The transaction was either canceled before submission or permanently canceled after submission.

  ## Constraints
    - This API must respond within 1 second.

  ## Recommendations
    - To simplify the management of the `request_id` and transaction hash pair, it is **strongly recommended** to leverage the Enclave APIs: Transaction Report API and Get Owner Verification Result API.
      - **Ensure Prompt Transaction Reporting**
        - Immediately after submitting the blockchain transaction and obtaining the transaction hash, call the Transaction Report API. This ensures you can:
          - Notify the Beneficiary VASP of the transaction result.
          - Store the transaction hash corresponding to the `request_id` in the Enclave.
      - **Retrieving the Transaction Hash From Enclave**
        - If the Beneficiary VASP later calls the Check Transaction Status API, use the Enclave's Get Owner Verification Result API to retrieve the stored transaction hash.
        - This transaction hash can then be used to query the real-time status of the blockchain transaction and respond to the Beneficiary VASP.
    - Leveraging this implementation allows your VASP to efficiently manage `request_id` and transaction hash mapping without a dedicated database, simplifying implementation while ensuring accurate and timely status handling.

  ## Environment Variable Configuration
    Set the following environment variables as per the guide to integrate the implemented API with the Enclave.
    - `VEGA_VERIFICATION_VERIFYNAME_TRANSACTION_API_PATH`: Implement this API at the desired path({`VASP_DEFINED_PATH_VERIFY_NAME_TRANSACTION_CHECK`}) and set the path in the variable.
    - `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: Use the API key provided during your VerifyVASP onboarding process to configure this variable.

    For a complete list of Enclave environment variables, [click here.](ref:verifyname-enclave-setup#/environment-variables)
api:
  file: VN_VASP_API_Spec.yaml
  operationId: verifyName-Transaction
hidden: false
---