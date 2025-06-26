---
title: Check Transaction Status API
api:
  file: VN_VASP_API_Spec.yaml
  operationId: verifyName-Transaction
hidden: false
---
이 API는 **수신 VASP** 역할을 위한 구현 요구사항입니다. 특정 트랜잭션의 현재 상태를 조회하여 반환합니다. 송신 VASP 측에서 트랜잭션 상태에 대한 비동기 업데이트가 지연되거나 누락된 경우 호출됩니다.

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