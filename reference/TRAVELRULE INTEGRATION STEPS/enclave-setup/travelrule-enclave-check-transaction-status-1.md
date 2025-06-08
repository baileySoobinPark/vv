---
title: Check Transaction Status API
api:
  file: TR_Enclave_API_KR_Spec.yaml
  operationId: travelrule-enclave-check-Transaction-Status
hidden: false
---
이 API는 수신 VASP가 송신 VASP로 트랜잭션의 상태 조회를 질의하기 위해 사용되는 API입니다. 수신 VASP측에서 사용자 검증을 완료했음에도 일정 시간 내에 송신 VASP로부터 트랜잭션 결과 Report 또는 Error Report가 없어 모니터링을 위해 전송 결과를 조회하는 경우에 사용합니다.

## 구현 정책

1. 수신 VASP는 입금을 감지한 경우, Verification Result 를 호출해 해당 트랜잭션과 매칭되는 검증 기록이 있는지 확인합니다.
2. 매칭되는 txHash가 없으면, 일정 시간 동안 송신 VASP가 Transaction Report API를 호출해 오기를 대기합니다.
3. 대기 시간이 지난 후에도 송신 VASP로부터의 트랜잭션 보고가 없는 경우, 수신 VASP는 Verification Result Lookup API를 다시 호출하여 후보 검증 건들을 조회할 수 있습니다.
4. 후보 검증 건을 좁히기 위해 감지된 입금 트랜잭션의 출금 주소(from address)와 입금 주소(to address)를 각각 originatorAccountNumber, beneficiaryAccountNumber 필터로 사용합니다.
5. 필터링된 검증 후보들에 대해 수신 VASP는 각각 Check Transaction Status API를 호출하여 실제 트랜잭션 ID 정보와의 매칭 여부를 확인할 수 있습니다.