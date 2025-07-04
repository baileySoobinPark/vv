---
title: Owner Verification Transaction Report API
api:
  file: VN_Enclave_API_Spec.yaml
  operationId: verifyname-transaction-report
hidden: false
---
송신 VASP가 블록체인에서 자산 이전 트랜잭션을 실행한 후, 해당 트랜잭션의 TxHash 또는 TxId를 수신 VASP에 보고 할 때 사용되는 API입니다. 사용자 검증 이후 자산 전송 트랜잭션을 실행한 VASP는 **반드시 이 API를 이용하여 트랜잭션 정보를 Central 서버로 보고**해야 합니다.

## 구현 정책

* 소유자 검증 후에 Result Report API를 이용하여 **최종 판단 결과를 VERIFIED로 보고한 건**에 대해서만 자산 이전 트랜잭션을 실행해야 하며, 트랜잭션 실행 후 반드시 본 API를 통해 TxHash 또는 TxId를 보고해야 합니다.
* 트랜잭션을 전송하고 TxHash가 **생성되는 즉시** 본 API를 호출하여 정보를 VerifyVASP Central 서버에 전달해야 합니다.
* 트랜잭션 전송이 실패하거나 취소되어 자산 이전이 중단된 경우, **즉시 결과 보고 API(Result Report API) 를 호출하여 Central 서버에 중단 상황을 보고**해야 합니다.

<br />

## API 명세