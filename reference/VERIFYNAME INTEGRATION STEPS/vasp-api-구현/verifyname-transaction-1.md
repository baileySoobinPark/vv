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
  file: VN_VASP_API_KR_Spec.yaml
  operationId: verifyName-Transaction
hidden: false
---
이 API는 **송신 VASP** 역할을 위한 구현 요구사항입니다. 지정한 자산 이전 트랜잭션의 현재 상태를 조회하여 반환합니다. 수신 VASP 측에서 트랜잭션 상태에 대한 비동기 업데이트가 지연되거나 누락된 경우 호출됩니다.

***

## 구현 가이드

### 기능 요구사항

#### 1. 요청 ID와 트랜잭션 Hash 맵핑

VASP는 송신 VASP 역할을 수행할 때 VerifyName 검증건의 요청 ID(`request_id`)와, 해당 검증과 관련된 자산 전송 트랜잭션의 Hash값을 쌍으로 맵핑하여 저장 및 관리해야 합니다.

#### 2. 온체인 트랜잭션 상태 조회 및 응답

요청 수신시 `request_id`와 맵핑된 트랜잭션 Hash를 기준으로 온체인 트랜잭션의 실시간 상태를 조회하여 결과를 `transaction_status` 필드에 아래 값 중 하나로 응답해야 합니다:

* `PENDING`: 아직 블록체인에 제출되지 않은 상태
* `PROCESSING`: 제출되었지만 아직 블록에 포함되지 않은 상태
* `WAIT-CONFIRM`: 블록에 포함되었으나 아직 finality가 확보되지 않은 상태
* `CONFIRMED`: 블록 생성 완료 및 finality 확보된 상태
* `CANCELED`: 제출 전 또는 후에 취소된 상태

### 제약 사항

이 API는 1초 이내에 응답해야 합니다.

### 환경 변수 설정

Enclave와의 정상 연동을 위해 아래와 같이 Enclave 환경 변수를 설정해야합니다.

* `VEGA_VERIFICATION_VERIFY_NAME_TRANSACTION_API_PATH`: 해당 API의 경로

***

## API 명세