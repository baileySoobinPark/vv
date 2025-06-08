---
title: Report Transaction Result API
api:
  file: TR_Enclave_API_KR_Spec.yaml
  operationId: travelrule-enclave-report-Transaction-Result
hidden: false
---
송신 VASP가 블록체인에서 자산 전송 트랜잭션을 실행한 후, 해당 트랜잭션의 txHash 또는 txId를 수신 VASP에 보고 할 때 사용되는 API입니다.

***

## 구현 정책

1. 사용자 검증이 완료된 건에 대해서만 자산 전송 트랜잭션을 실행해야 하며, 실행 후 반드시 본 API를 통해 txHash 또는 txId를 보고해야 합니다.
2. 트랜잭션 해시가 생성되는 즉시 본 API를 호출하여 정보를 VerifyVASP 중앙 서버에 전달해야 합니다.
3. 트랜잭션 finality가 확보되지 않아 자산 전송이 실패할 경우, 즉시 오류 보고 API(Report Error API) 를 호출하여 중앙 서버에 오류 상황을 보고해야 합니다.

***

## API 명세