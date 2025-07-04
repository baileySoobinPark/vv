---
title: Owner Verification Result Report API
api:
  file: VN_Enclave_API_Spec.yaml
  operationId: verifyname-owner-verification-result-report
hidden: false
---
이 API는 소유자 검증 결과를 수신한 VASP가 최종 트랜잭션 수행 결정을 상대 VASP에게 Report할 때 호출됩니다. 소유자 검증을 요청한 VASP는 항목별 검증 결과를 바탕으로 **최종적으로 종합 판단한 검증 결과를 반드시 이 API를 호출하여 Central 서버에 보고**해야 합니다.

## 구현 정책

* 소유자 검증 결과를 수신한 VASP는 반드시 최종 트랜잭션 수행 결정 결과를 보고해야 합니다.
* 최종적으로 판단한 검증 결과가 DENIED 또는 ERROR인 경우, `reason` 및 `message` 필드에 그 사유를 명확히 기재해야 합니다.

***

## API 명세