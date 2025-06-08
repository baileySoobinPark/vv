---
title: Check Trasnaction Status API
api:
  file: TR_VASP_API_KR_Spec.yaml
  operationId: travelrule-check-Transaction-Status
hidden: false
---
VASP는 TravelRule 프로토콜 내에서 송신 VASP와 수신 VASP의 역할을 모두 수행해야 합니다. 이 API는 송신 VASP 역할을 위한 구현 요구사항입니다. 수신 VASP가 (Transaction Report 누락 등으로 인한)미확인 온체인 입금을 인지한 후 송신 VASP로 관련 트랜잭션의 상태 조회를 요청하는 경우 Enclave로부터 이 API가 호출됩니다.

***

## 구현 가이드

### 기능 요구사항

#### 1. verificationUuid와 트랜잭션 해시 쌍 저장 (상시)

* 송신 VASP는 검증 요청 시 응답으로 발급받은 `verificationUuid`와, 해당 검증 응답에 따른 후속조치로 실행한 트랜잭션의 해시 값을 연결하여 데이터베이스 등에 저장 및 관리해야 합니다.

#### 2. 트랜잭션 상태 조회 및 응답 (API 호출 시)

* 상태 조회 요청시 요청의 트랜잭션 해시로 해당 트랜잭션의 블록체인 상의 상태를 조회하고, `transactionStatus` 필드에 아래 값 중 하나로 응답해야 합니다:
  * `PENDING`: 아직 블록체인에 제출되지 않은 상태
  * `PROCESSING`: 제출되었지만 아직 블록에 포함되지 않은 상태
  * `WAIT-CONFIRM`: 블록에 포함되었으나 아직 finality가 확보되지 않은 상태
  * `CONFIRMED`: 블록 생성 완료 및 finality 확보된 상태
  * `CANCELED`: 제출 전 또는 후에 취소된 상태

<br />

### 제약 조건

이 API는 1초 이내에 응답해야 합니다.

### 구현 권장 사항

`verificationUuid`와 트랜잭션 해시 관리를 단순화하기 위해 Enclave의 다음 API를 활용하는 것을 권장합니다:

* **Report Transaction Result API**: 트랜잭션 실행 직후 해시를 Enclave에 저장하고 수신 VASP에 리포트
* **Get Verification Result API**: 수신 VASP의 요청 시 저장된 트랜잭션 해시를 조회하여 실시간 상태 확인에 사용

Enclave를 활용하면 별도 DB 없이도 효율적인 매핑 및 상태 응답 처리가 가능합니다.

### Enclave 연동 설정

다음 환경변수를 설정해야 Enclave와 연동됩니다:

* `VEGA_VERIFICATION_TRANSACTION_API_PATH`: 해당 API 구현 경로
* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: 온보딩 시 발급받은 API 키