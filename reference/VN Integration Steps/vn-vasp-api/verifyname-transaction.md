---
title: Check Transaction Status API
api:
  file: VN_VASP_API_Spec.yaml
  operationId: verifyName-Transaction
hidden: false
---
VASP는 VerifyName 프로토콜 내에서 송신 VASP와 수신 VASP의 역할을 모두 수행합니다. 이 API는 **송신 VASP** 역할을 위한 구현 요구사항입니다. 특정 트랜잭션의 현재 상태를 조회하여 반환합니다. 트랜잭션 전송 후 해시 값에 대한 보고가 지연되거나 누락된 경우 수신 VASP에 의해 호출됩니다.

***

## 구현 가이드

### 기능 요구사항

#### 1. 요청 ID와 트랜잭션 Hash 맵핑

VASP는 송신 VASP 역할을 수행할 때 VerifyName 검증 건의 요청 ID(`request_id`)와, 관련된 트랜잭션의 해시값을 쌍으로 맵핑하여 저장 및 관리해야 합니다.

#### 2. 온체인 트랜잭션 상태 조회 및 응답

API 호출을 받았을 경우 `request_id`와 맵핑된 트랜잭션 해시를 기준으로 온체인 트랜잭션의 실시간 상태를 조회하여 그 결과를 `transaction_status` 필드에 **반드시 아래 값 중 하나로 응답**해야 합니다:

* `PENDING`: 아직 블록체인에 트랜잭션이 제출되지 않은 상태
* `PROCESSING`: 트랜잭션이 제출되었지만 아직 블록에 포함되지 않은 상태
* `WAIT-CONFIRM`: 블록에 포함되었으나 아직 finality가 확보되지 않은 상태
* `CONFIRMED`: 트랜잭션 채굴 완료 및 finality 확보된 상태
* `CANCELED`: 트랜잭션 전송을 하지 않기로 결정했거나 영구히 취소된 상태

### 제약 사항

이 API는 1초 이내에 응답해야 합니다.

### 주의 사항

* 반드시 `transaction_status` 필드에 값을 반환해야 합니다.
* 최종 판단이 결정된 경우에는 반드시 `verification_result` 필드에 그 결과 값을 반환해야 합니다.
* 아직 최종 판단이 결정되지 않은 경우에는 `transaction_status` 필드를 `PENDING`으로 반환해야 합니다.
* 최종 판단 결과가 DENIED 혹은 ERROR로 트랜잭션을 전송하지 않기로 결정했다면 반드시 그 사유를 reason 필드에 입력하여 반환해야 하며, `transaction_status` 필드를 `CANCELED`로 반환해야 합니다.

### 환경 변수 설정

Enclave와의 연동을 위해 아래와 같이 Enclave 환경 변수를 설정해야합니다.

* `VEGA_VERIFICATION_VERIFY_NAME_TRANSACTION_API_PATH`: 해당 API의 경로
* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: API 인증을 위한 인증 토큰 값
* `VEGA_VERIFICATION_AUTHORIZATION_KEY`: API 인증 토큰을 전달할 header key

***

## API 명세