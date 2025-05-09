---
title: Callback API
excerpt: >
  The Callback API serves as a common interface for handling various
  asynchronous flows. Depending on the callbackType, it may be invoked when your
  VASP acts as either the Originating VASP or the Beneficiary VASP. Your
  implementation must function correctly in all possible cases. The Enclave will
  invoke this API when a counterparty VASP triggers a Report APIs.


  ## Functional Requirements
    **1. Callback Type Support**
    The implementation must route the request to appropriate business logic for each callbackType values. The following are the possible values for the callbackType field and the scenarios in which they are used:
    - **`OWNER_VERIFICATION_TX_REPORT`**: callbackType for Beneficiary VASP role. This callback is triggered by Enclave when the Originating VASP sends a transaction result or error report.

  `OWNER_VERIFICATION_TX_REPORT` type must be implemented as mandatory
  processing logic.

    **2. OWNER_VERIFICATION_TX_REPORT Callback Handling (as Beneficiary VASP)**
    When a transaction report is received, your VASP can
    - Match the reported on-chain transaction hash with the actual deposit to the beneficiary address.
    - Confirm that the asset transfer has been requested and log the details.

    When an error report is received, your VASP can
    - Cancel the associated asset transfer request.          
    - Stop tracking the transaction and log the error for auditing purposes.

  <details>
    <summary>Example of Request Body for OWNER_VERIFICATION_TX_REPORT callback type.</summary>

    ``` json
      // VERIFIED
      {
        "callbackType": "OWNER_VERIFICATION_TX_REPORT",
        "data": {
          "request_id": "64ab871b-14a3-47df-9b80-368e29fe8181",
          "reported_result": "VERIFIED",
          "ordered_at": "2025-04-21T04:40:28.536Z",
          "tx_hash": "0xd231a7c7ff1edba061e3fbde26fe0e567fde0d2c40ff40ad1a9f3bffd999f128"
        }
      }

      or

      // DENIED
      {
        "callbackType": "OWNER_VERIFICATION_TX_REPORT",
        "data": {
          "request_id": "64ab871b-14a3-47df-9b80-368e29fe8181",
          "reported_result": "DENIED",
          "ordered_at": "2025-04-21T04:40:28.536Z",
          "reason": "MISMATCH-TICKER"
        }
      }

      or

      // ERROR
      {
        "callbackType": "OWNER_VERIFICATION_TX_REPORT",
        "data": {
          "request_id": "64ab871b-14a3-47df-9b80-368e29fe8181",
          "reported_result": "ERROR",
          "ordered_at": "2025-04-21T04:40:28.536Z",
          "reason": "TRANSFER-ERROR",
          "message": "transfer error"
        }
      }
    ```
  </details>


  ## Environment Variable Configuration
    Set the following environment variables as per the guide to integrate the implemented API with the Enclave. For a complete list of Enclave environment variables, click [here.]()
    - `VEGA_VERIFICATION_CALLBACK_API_PATH`: Implement this API at the desired path({VASP_DEFINED_PATH_CALLBACK}) and set the path in the variable.
    - `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: Set this variable to the API key provided during your VerifyVASP onboarding process.
api:
  file: 2025_05_09_VN_VASP_API_Spec.yaml
  operationId: verifyNameCallback
hidden: false
---