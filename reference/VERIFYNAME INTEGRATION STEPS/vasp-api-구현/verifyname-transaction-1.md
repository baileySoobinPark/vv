---
title: Check Transaction Status API
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

VASP는