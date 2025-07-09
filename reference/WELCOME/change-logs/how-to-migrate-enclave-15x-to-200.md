---
title: How to migrate Enclave 1.5.x to 2.0.0
deprecated: false
hidden: false
metadata:
  robots: index
---
# Database Table 추가

* Verify Name V2 API 를 사용하기 위해서 owner\_verifications 테이블이 추가되었습니다.
* 아래 환경 변수 설정에서 VEGA\_ENCLAVE\_MODE 에 VN2 를 넣을 경우 사전에 owner\_verifications 테이블이 추가되어 있어야 합니다.
* Create Table SQL
  * [https://verifyvasp.readme.io/reference/enclave-데이터베이스-생성#/verifyname-필수-테이블-생성-쿼리](https://verifyvasp.readme.io/reference/enclave-%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-%EC%83%9D%EC%84%B1#/verifyname-%ED%95%84%EC%88%98-%ED%85%8C%EC%9D%B4%EB%B8%94-%EC%83%9D%EC%84%B1-%EC%BF%BC%EB%A6%AC)

# Verify Name V2 를 위한 VASP API 구현

* VN2 응답을 위한 API 의 구현이 필요합니다.
  * [https://verifyvasp.readme.io/reference/verifyname-request-verification#/](https://verifyvasp.readme.io/reference/verifyname-request-verification#/)
* VN2 check transaction status 응답을 위한 API 의 구현이 필요합니다.
  * [https://verifyvasp.readme.io/reference/verifyname-transaction#/](https://verifyvasp.readme.io/reference/verifyname-transaction#/)
* VASP backend 에 구현하신 Callback API 에서 VN2 를 위해 추가된 callbackType “OWNER\_VERIFICATION\_RESULT\_REPORT” 와 “OWNER\_VERIFICATION\_TX\_REPORT” 에 대한 처리 로직을 구현하셔야 합니다.
  * [https://verifyvasp.readme.io/reference/verifyname-callback#/](https://verifyvasp.readme.io/reference/verifyname-callback#/)

# 환경 변수 설정

* **(BREAKING CHANGE) 필수 환경변수**
  * VEGA\_ENCLAVE\_MODE=TR,VN2
    * Enclave Mode 설정이 추가되었습니다.
    * mode 는 TR, VN1\_CALL, VN1\_RESPONSE, VN2 중 하나 이상을 콤마로 연결해서 설정해야 합니다.
    * 설정하지 않고 실행한 경우 아래와 같은 에러를 출력하고 enclave 가 종료 됩니다.
    ```jsx
    No valid enclave acceptable mode provided.
    Please set VEGA_ENCLAVE_MODE. For example, TR,VN1_CALL,VN1_RESPONSE,VN2 or any combination of these.
    ```
* “[Verify Name V2 를 위한 VASP API 구현](https://verifyvasp.readme.io/reference/how-to-migrate-enclave-15x-to-200#verify-name-v2-%EB%A5%BC-%EC%9C%84%ED%95%9C-vasp-api-%EA%B5%AC%ED%98%84) “ 에서 VASP API 를 구현한 경우 아래 환경변수에 endpoint url 을 설정해야 합니다.
  * VEGA\_VERIFICATION\_VERIFY\_NAME\_V2\_API\_PATH="[http://xxx/verify-name](http://xxx/verify-name)"
    * VN2 응답을 위한 VASP API PATH
  * VEGA\_VERIFICATION\_VERIFY\_NAME\_TRANSACTION\_API\_PATH="[http://xxx/verify-name/tx/inquiry](http://xxx/verify-name/tx/inquiry)"
    * VN2 check transaction status 응답을 위한 VASP API PATH