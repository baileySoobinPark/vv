---
title: Report Error API
api:
  file: TR_Enclave_API_KR_Spec.yaml
  operationId: travelrule-enclave-report-Error
hidden: false
---
송신 VASP가 검증 실패 또는 처리 프로세스상의 오류로 인해 자산 전송을 취소하고 해당 오류를 수신 VASP에게 Report할 때 사용하는 API입니다. 이 API의 호출로 인해 수신 VASP는 해당 가상자산 전송 건이 실패했음을 인지하고 모니터링 중지 및 사용자 안내를 진행할 수 있습니다.

## 구현 정책

1. 사용자 검증이 완료된 이후 송신 VASP 측에서 자산 전송이 취소된 경우에만 본 API를 호출하여 오류 상황을 보고해야 합니다.
2. 실제로 송신 VASP가 해당 자산 전송을 영구적으로 취소한 경우에만 호출해야 합니다.
3. 요청 시 취소 사유를 명확히 명시해야 하며, 일반적인 사유로는 수신자가 제재 리스트(Sanction List)에 포함된 경우 등이 있습니다.