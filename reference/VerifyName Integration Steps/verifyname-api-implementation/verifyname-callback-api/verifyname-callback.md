---
title: Callback API
excerpt: >
  The Callback API serves as a common interface for handling various
  asynchronous flows. Depending on the callbackType, it may be invoked when your
  VASP acts as either the Originating VASP or the Beneficiary VASP. Your
  implementation must function correctly in all possible cases. The Enclave will
  invoke this API when a counterparty VASP triggers a Report APIs.


  ## Functional Requirements
    **1. Callback Types Support**
    The implementation must route the request to appropriate business logic for each callbackType values. The following are the possible values for the callbackTypes field and the scenarios in which they are used:
    - **`OWNER_VERIFICATION_RESULT_REPORT`**: This callback is triggered by the Enclave when the final Owner Verification result is reported.
    - **`OWNER_VERIFICATION_TX_REPORT`**: This callback is triggered by the Enclave when the Originating VASP reports the execution of a transfer transaction.

    `OWNER_VERIFICATION_RESULT_REPORT` and `OWNER_VERIFICATION_TX_REPORT` type must be implemented as mandatory processing logic.

    **2.Callback Handling**
    When a Owner Verification result report is received, your VASP can perform the following actions.
    - If the result is `VERIFIED`, your VASP can proceed with the asset transfer.
    - If the result is `DENIED` or `ERROR`, your VASP can stop the asset transfer or deposit process and notify it to the Originator.

    When a Transaction result report is received, your VASP can perform the following actions.
    - Match the reported on-chain transaction hash with the actual deposit to the beneficiary address.
    - Confirm that the asset transfer has been requested and log the details.

  <details>
    <summary>Example of Request Body for OWNER_VERIFICATION_RESULT_REPORT callback type.</summary>

    ``` json
      // VERIFIED
      {
        "callbackType": "OWNER_VERIFICATION_RESULT_REPORT",
        "data": {
          "request_id": "64ab871b-14a3-47df-9b80-368e29fe8181",
          "reported_result": "VERIFIED",
        }
      }

      or

      // DENIED
      {
        "callbackType": "OWNER_VERIFICATION_RESULT_REPORT",
        "data": {
          "request_id": "64ab871b-14a3-47df-9b80-368e29fe8181",
          "reported_result": "DENIED",
          "reason": "MISMATCH-TICKER"
          "message": "Ticker is mismatched."
        }
      }

      or

      // ERROR
      {
        "callbackType": "OWNER_VERIFICATION_RESULT_REPORT",
        "data": {
          "request_id": "64ab871b-14a3-47df-9b80-368e29fe8181",
          "reported_result": "ERROR",
          "reason": "TRANSFER-ERROR",
          "message": "Transfer is failed."
        }
      }
    ```
  </details>

  <details>
    <summary>Example of Request Body for OWNER_VERIFICATION_TX_REPORT callback type.</summary>

    ``` json
      {
        "callbackType": "OWNER_VERIFICATION_TX_REPORT",
        "data": {
          "request_id": "64ab871b-14a3-47df-9b80-368e29fe8181",
          "tx_hash": "0xd231a7c7ff1edba061e3fbde26fe0e567fde0d2c40ff40ad1a9f3bffd999f128"
        }
      }
    ```
  </details>


  ## Constraints
    - This API must respond within 1 second.
    - Only the HTTP status code 200 OK can be returned. Other response status codes are not allowed.
    - To maintain data consistency and reliability, the Callback API must guarantee idempotency. 
      - This ensures that if the same Callback API request is received multiple times from the Enclave, the data state and response remain unchanged after the initial call. 
      - For example, you can implements your VASP to ignore the duplicate requests.

  ## Recommendations
    Since the API response must be returned as quickly as possible, time-consuming tasks within the Callback API should be handled asynchronously.

  ## Environment Variable Configuration
    Set the following environment variables as per the guide to integrate the implemented API with the Enclave.
    - `VEGA_VERIFICATION_CALLBACK_API_PATH`: Implement this API at the desired path({VASP_DEFINED_PATH_CALLBACK}) and set the path in the variable.
    - `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: Set this variable to the API key provided during your VerifyVASP onboarding process.

    For a complete list of Enclave environment variables, [click here.](ref:verifyname-enclave-setup/environment-variables)
api:
  file: VN_VASP_API_Spec.yaml
  operationId: verifyName-Callback
hidden: false
---