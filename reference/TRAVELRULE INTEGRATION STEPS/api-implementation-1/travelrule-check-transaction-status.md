---
title: Check Trasnaction Status API
api:
  file: TR_VASP_API_Spec.yaml
  operationId: travelrule-check-Transaction-Status
hidden: false
---
VASP는 TravelRule 프로토콜 상에서 송신 VASP와 수신 VASP의 역할을 모두 수행해야 합니다. 본 API는 수신 VASP 역할에 해당하는 구현 요구사항입니다. 송신 VASP가 Transaction Report 누락 등으로 인한 미확인 입금을 인지했을 때, 해당 트랜잭션의 상태를 조회하고자 송신 VASP에 요청하는 경우 Enclave를 통해 호출됩니다.

***

## 구현 가이드

### 기능 요구사항

#### 1. verificationUuid와 트랜잭션 해시 값 저장 (상시)

* 송신 VASP는 검증 요청 시 응답으로 발급받은 `verificationUuid`와, 해당 검증 응답에 따른 후속조치로 실행한 트랜잭션의 해시 값을 연결할 수 있어야 합니다.

#### 2. 트랜잭션 상태 조회 및 응답 (API 호출 시)

* 상태 조회 요청시 요청의 트랜잭션 해시로 해당 트랜잭션의 블록체인 상의 상태를 조회하고, `transactionStatus` 필드에 아래 값 중 하나로 응답해야 합니다:
  * `PENDING`: 아직 블록체인에 제출되지 않은 상태
  * `PROCESSING`: 제출되었지만 아직 블록에 포함되지 않은 상태
  * `WAIT-CONFIRM`: 블록에 포함되었으나 아직 finality가 확보되지 않은 상태
  * `CONFIRMED`: 블록 생성 완료 및 finality 확보된 상태
  * `CANCELED`: 제출 전 또는 후에 취소된 상태

### 제약 조건

이 API는 1초 이내에 응답해야 합니다.

### 구현 권장사항

다음과 같이 Enclave API들을 활용하여 별도의 데이터베이스 테이블 구축 없이도 `verificationUuid`와 트랜잭션 해시 쌍 데이터를 관리할 수 있습니다.

* **Report Transaction Result API**: 트랜잭션 실행 직후 호출하여 트랜잭션 해시를 Enclave 데이터베이스에 저장하고 수신 VASP에 Report
* **Get Verification Result API**: 트랜잭션 상태 조회 요청 인입 시 `verificationUuid`로 Encalve 데이터베이스에 저장된 트랜잭션 해시를 조회하여 실시간 온체인 상태 확인에 사용

### Enclave 연동 설정

Enclave와의 정상 연동을 위해 아래 환경 변수를 설정해야 합니다.

* `VEGA_VERIFICATION_TRANSACTION_API_PATH`: 해당 API 구현 경로
* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: VerifyVASP 온보딩 시 발급받은 API 키

***

## API 명세