---
title: How to migrate Enclave 1.5.x to 2.0.0
deprecated: false
hidden: false
metadata:
  robots: index
---
# Database Table Addition

* **(BREAKING CHANGE) owner\_verifications table added**
  * The owner\_verifications table has been added to support the VerifyName API.
  * Regardless of the VerifyName API version, this table must be created in order to use the VerifyName Protocol.
* Create Table SQL
  * [https://verifyvasp.readme.io/reference/enclave-데이터베이스-생성#/verifyname-필수-테이블-생성-쿼리](https://verifyvasp.readme.io/reference/enclave-%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-%EC%83%9D%EC%84%B1#/verifyname-%ED%95%84%EC%88%98-%ED%85%8C%EC%9D%B4%EB%B8%94-%EC%83%9D%EC%84%B1-%EC%BF%BC%EB%A6%AC)

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
  * VEGA\_ENCLAVE\_MODE=TR,VN2
    * Added a configuration for Enclave Mode.
    * mode must be one or more of the following, separated by commas:
      * TR, VN1\_CALL, VN1\_RESPONSE, VN2
    * If not configured, the enclave will print an error and terminate, for example:
    ```text
    No valid enclave acceptable mode provided.
    Please set VEGA_ENCLAVE_MODE. For example, TR,VN1_CALL,VN1_RESPONSE,VN2 or any combination of these.
    ```
* If you have implemented the VASP API for VerifyName V2 (as described above), you must configure the endpoint URL in the following environment variable:
  * VEGA\_VERIFICATION\_VERIFY\_NAME\_V2\_API\_PATH="[http://xxx/verify-name](http://xxx/verify-name)"
    * API path for VASP responses in VN2.