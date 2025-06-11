---
title: Request User Account Verification API
api:
  file: TR_Enclave_API_Spec.yaml
  operationId: travelrule-Enclave-User-Account-Verification
hidden: false
---
송신 VASP가 수신 VASP에게 수신자 검증 요청을 전송하기 위한 API입니다. 요청이 수신 VASP로부터 정상 접수되는 경우 해당 요청을 고유하게 식별할 수 있는 verificationUuid가 응답으로 반환됩니다.

***

## 비동기 API

사용자 검증 프로세스의 복잡도를 고려하여 이 API는 비동기 방식으로 동작합니다. 요청의 응답은 오직 요청건에 대한 고유 식별자인 verificationUuid만을 포함하며, 실제 검증 결과는 일정 시간 이후 해당 UUID를 포함한 Callback API 호출을 통해 수신하거나 Get Verification Result API를 호출하여 확인할 수 있습니다.

***

## API 명세