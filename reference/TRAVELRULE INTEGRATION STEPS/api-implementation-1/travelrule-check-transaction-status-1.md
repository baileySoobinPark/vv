---
title: Check Trasnaction Status API
api:
  file: TR_VASP_API_KR_Spec.yaml
  operationId: travelrule-check-Transaction-Status
hidden: false
---
본 API는 귀사의 VASP가 **송신 VASP(Originating VASP)** 역할을 수행할 때 구현해야 합니다.\
송신자가 제출한 블록체인 트랜잭션의 상태를 수신 VASP가 확인할 수 있도록 현재 상태를 조회하여 응답하는 것이 목적입니다.

이 API는 수신 VASP가 비동기 리포트를 받지 못한 경우, Enclave를 통해 호출됩니다.

### 기능 요건

**1. verificationUuid와 트랜잭션 해시 매핑**

* 송신 VASP는 이전 검증 요청 시 발급받은 `verificationUuid`에 대해, 실제 전송한 트랜잭션의 해시 값을 연결해 관리해야 합니다.

**2. 트랜잭션 상태 조회 및 응답**

* 트랜잭션 해시를 기반으로 현재 블록체인 상의 상태를 조회하고, `transactionStatus` 필드에 아래 값 중 하나로 응답해야 합니다:
  * `PENDING`: 아직 블록체인에 제출되지 않은 상태
  * `PROCESSING`: 제출되었지만 아직 마이닝되지 않은 상태
  * `WAIT-CONFIRM`: 마이닝되었지만 아직 finality가 확보되지 않은 상태
  * `CONFIRMED`: 마이닝 완료 및 finality 확보된 상태
  * `CANCELED`: 제출 전 또는 후에 취소된 상태

### 제약 조건

* 1초 이내에 응답해야 합니다.

### 구현 권장 사항

`verificationUuid`와 트랜잭션 해시 관리를 단순화하기 위해 Enclave의 다음 API를 활용하는 것을 권장합니다:

* **Report Transaction Result API**: 트랜잭션 실행 직후 해시를 Enclave에 저장하고 수신 VASP에 리포트
* **Get Verification Result API**: 수신 VASP의 요청 시 저장된 트랜잭션 해시를 조회하여 실시간 상태 확인에 사용

Enclave를 활용하면 별도 DB 없이도 효율적인 매핑 및 상태 응답 처리가 가능합니다.

### Enclave 연동 설정

다음 환경변수를 설정해야 Enclave와 연동됩니다:

* `VEGA_VERIFICATION_TRANSACTION_API_PATH`: 해당 API 구현 경로
* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: 온보딩 시 발급받은 API 키