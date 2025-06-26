---
title: Report Error API
api:
  file: TR_Enclave_API_Spec.yaml
  operationId: travelrule-enclave-report-Error
hidden: false
---
송신 VASP가 검증 실패 또는 내부 처리 오류 등의 사유로 자산 이전을 중단한 사실을 수신 VASP에 통지하기 위해 사용하는 API입니다. 본 API 호출을 통해 수신 VASP는 해당 트랜잭션이 중단되었음을 인지하고, 관련 모니터링을 종료하거나 고객에게 안내할 수 있습니다.

## 구현 정책

1. 사용자 검증이 완료된 이후 송신 VASP 측에서 자산 이전이 중단된 경우에만 본 API를 호출하여 중단 상황을 보고해야 합니다.
2. 실제로 송신 VASP가 해당 자산 이전을 영구적으로 취소한 경우에만 호출해야 합니다.
3. API 호출 시 취소 사유를 명확히 명시해야 합니다.

***

## API 명세