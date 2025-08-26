---
title: How to migrate Enclave 1.5.x to 2.0.0
deprecated: false
hidden: false
metadata:
  robots: index
---
# Database Table Addition

* **(BREAKING CHANGE) owner_verifications table added**
  * The owner_verifications table has been added to support the VerifyName API.
  * Regardless of the VerifyName API version, this table must be created in order to use the VerifyName Protocol.
* Create Table SQL
  * [https://docs.verifyvasp.com/reference/verifyname-database-setup#verifyname-required-table-creation-queries](https://docs.verifyvasp.com/reference/verifyname-database-setup#verifyname-required-table-creation-queries)

<br />

# VASP API Implementation for VerifyName V2

* To use VerifyName V2, you need to implement an API that can respond with a VN2 response.
  * [https://verifyvasp.readme.io/reference/verifyname-request-verification#/](https://verifyvasp.readme.io/reference/verifyname-request-verification#/)
* In the Callback API previously implemented in your VASP backend, you must add handling logic for the following callbackTypes introduced for VerifyName V2:
  * `OWNER_VERIFICATION_RESULT_REPORT`
  * `OWNER_VERIFICATION_TX_REPORT`
  * [https://verifyvasp.readme.io/reference/verifyname-callback#/](https://verifyvasp.readme.io/reference/verifyname-callback#/)

<br />

# Environment Variable Configuration

* **(BREAKING CHANGE) Required Environment Variable**
  * VEGA_ENCLAVE_MODE=TR,VN2
    * Added a configuration for Enclave Mode.
    * mode must be one or more of the following, separated by commas:
      * TR, VN1_CALL, VN1_RESPONSE, VN2
    * If not configured, the enclave will print an error and terminate, for example:
    ```text
    No valid enclave acceptable mode provided.
    Please set VEGA_ENCLAVE_MODE. For example, TR,VN1_CALL,VN1_RESPONSE,VN2 or any combination of these.
    ```
* If you have implemented the VASP API for VerifyName V2 (as described above), you must configure the endpoint URL in the following environment variable:
  * VEGA_VERIFICATION_VERIFY_NAME_V2_API_PATH="http://xxx/verify-name"
    * API path for VASP responses in VN2.
