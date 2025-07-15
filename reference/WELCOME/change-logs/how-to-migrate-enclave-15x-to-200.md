---
title: How to migrate Enclave 1.5.x to 2.0.0
deprecated: false
hidden: false
metadata:
  robots: index
---
# Database Table 추가

* **(BREAKING CHANGE) owner\_verifications 테이블 추가**
  * VerifyName API를 사용하기 위해서 owner\_verifications 테이블이 추가되었습니다.
  * VerifyName API의 버전과 상관없이 VerifyName 프로토콜을 사용하려면 반드시 해당 테이블이 생성되어야 합니다.
* Create Table SQL
  * [https://verifyvasp.readme.io/reference/enclave-데이터베이스-생성#/verifyname-필수-테이블-생성-쿼리](https://verifyvasp.readme.io/reference/enclave-%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-%EC%83%9D%EC%84%B1#/verifyname-%ED%95%84%EC%88%98-%ED%85%8C%EC%9D%B4%EB%B8%94-%EC%83%9D%EC%84%B1-%EC%BF%BC%EB%A6%AC)

<br />

# VerifyName V2 를 위한 VASP API 구현

* VerifyName V2를 사용하고 싶은 경우 VN2 응답을 위한 API 구현이 필요합니다.
  * [https://verifyvasp.readme.io/reference/verifyname-request-verification#/](https://verifyvasp.readme.io/reference/verifyname-request-verification#/)
* VASP backend 에 구현했던 Callback API 에서 VerifyName V2 를 위해 추가된 callbackType `OWNER_VERIFICATION_RESULT_REPORT` 와 `OWNER_VERIFICATION_TX_REPORT` 에 대한 처리 로직을 구현해야 합니다.
  * [https://verifyvasp.readme.io/reference/verifyname-callback#/](https://verifyvasp.readme.io/reference/verifyname-callback#/)

<br />

# 환경 변수 설정

* **(BREAKING CHANGE) 필수 환경변수 설정**
  * VEGA\_ENCLAVE\_MODE=TR,VN2
    * Enclave Mode 설정이 추가되었습니다.
    * mode 는 TR, VN1\_CALL, VN1\_RESPONSE, VN2 중 하나 이상을 콤마로 연결해서 설정해야 합니다.
    * 설정하지 않고 실행한 경우 아래와 같은 에러를 출력하고 enclave 가 종료 됩니다.
    ```text
    No valid enclave acceptable mode provided.
    Please set VEGA_ENCLAVE_MODE. For example, TR,VN1_CALL,VN1_RESPONSE,VN2 or any combination of these.
    ```
* 상기한 `VerifyName V2 를 위한 VASP API 구현` 단계에서 VASP API 를 구현한 경우 아래 환경변수에 endpoint url 을 설정해야 합니다.
  * VEGA\_VERIFICATION\_VERIFY\_NAME\_V2\_API\_PATH="[http://xxx/verify-name](http://xxx/verify-name)"
    * VN2 응답을 위한 VASP API PATH