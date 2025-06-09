---
title: Owner Verification Result Report API
api:
  file: VN_Enclave_API_KR_Spec.yaml
  operationId: verifyname-owner-verification-result-report
hidden: false
---
이 API는 소유자 검증 결과를 수신한 VASP가 최종 트랜잭션 실행 결정을 상대 VASP에게 Report할 때 호출됩니다. 소유자 검증을 요청한 VASP는 Callback API를 통해 최종 검증 결과를 수신하며, 이후 이 API를 호출하여 해당 결과를 중앙 서버에 보고해야 합니다.

## 구현 정책

* 소유자 검증 결과를 수신한 VASP는 반드시 최종 전송 실행 결과를 보고해야 합니다.
* 최종 결과가 DENIED 또는 ERROR인 경우, `code` 및 `message` 필드에 그 사유를 명확히 기재해야 합니다.