---
title: Owner Verification Transaction Report API
api:
  file: VN_Enclave_API_Spec.yaml
  operationId: verifyname-transaction-report
hidden: false
---
송신 VASP가 블록체인에서 자산 이전 트랜잭션을 실행한 후, 해당 트랜잭션의 Txhash 또는 TxId를 수신 VASP에 보고 할 때 사용되는 API입니다.

## 구현 정책

* 사용자 검증이 완료된 건에 대해서만 자산 이전 트랜잭션을 실행해야 하며, 실행 후 반드시 본 API를 통해 Txhash 또는 TxId를 보고해야 합니다.
* 트랜잭션 해시가 생성되는 즉시 본 API를 호출하여 정보를 VerifyVASP Central 서버에 전달해야 합니다.
* 트랜잭션 finality가 확보되지 않아 자산 이전이 중단될 경우, 즉시 오류 보고 API(Report Error API) 를 호출하여 Central 서버에 중단 상황을 보고해야 합니다.

<br />

## API 명세