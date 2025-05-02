---
title: Callback API
excerpt: >
  The Callback API serves as a common interface for handling various
  asynchronous flows. Depending on the callbackType, it may be invoked when your
  VASP acts as either the Originating VASP or the Beneficiary VASP. Your
  implementation must function correctly in all possible cases. The Enclave will
  invoke this API when a counterparty VASP triggers a Report APIs.


  ### Functional Requirements

  ** 1. Callback Type Support**

  The implementation must route the request to appropriate business logic for
  each callbackType values. The following are the possible values for the
  callbackType field and the scenarios in which they are used:
    - **VERIFICATION_RESULT**: callbackType for Originating VASP role. This callback is triggered by Enclave when it the user verification process is terminated and result is delivered to the Enclave asynchronously.
    - **TX_REPORT**: callbackType for Beneficiary VASP role. This callback is triggered by Enclave when the Originating VASP sends a transaction result report.
    - **ERROR_REPORT**: callbackType for Beneficiary VASP role. This callback is triggered by Enclave when the Originating VASP sends an error report.
  `VERIFICATION_RESULT`, `TX_REPORT`, and `ERROR_REPORT` types must be
  implemented as mandatory processing logic.


  ### 2. VERIFICATION_RESULT Callback Type Handling (as Originating VASP)

  When a verification result is received, your VASP must perform further
  required actions in accordance with the verification result. For example,
    - For successful verification, proceed with either initiating beneficiary verification on the Originating VASP side or executing the on-chain transaction.
    - For failed verification, cancel the associated asset transfer and notify the originator user of the failure with proper message. data.reason field can be refered to generate the failure messages.
    Example of Request Body for VERIFICATION_RESULT callback type with VERIFIED result

      <details>
        <summary>Example of Request Body for `VERIFICATION_RESULT` callback type with `VERIFIED` result</summary>

        ```json
        {
          "callbackType": "VERIFICATION_RESULT",
          "data": {
            "result": "VERIFIED",
            "verificationUuid": "ecb457e3-2307-4e72-8a42-16a3774e154b"
          }
        }
        ```
      </details>
api:
  file: 2025_05_02_VASP_API_Spec.yaml
  operationId: callback
hidden: false
---