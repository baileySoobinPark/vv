---
title: User Account Verification API
api:
  file: TR_Enclave_API_KR_Spec.yaml
  operationId: travelrule-Enclave-User-Account-Verification
hidden: false
---
송신 VASP가 수신 VASP에게 수신 계좌 검증 요청을 전송하기 위한 API입니다. 지정된 지갑 주소가 수신 VASP 소유임을 검증한 결과가 응답으로 반환됩니다.

***

## 검증 결과

검증 결과는 응답의 `accountVerificationResult` 필드에서 확인할 수 있습니다. 결과가 `DENIED`인 경우, reason 필드에 아래 중 하나의 실패 사유가 반환됩니다.