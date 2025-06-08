---
title: Check Transaction Status API
api:
  file: TR_Enclave_API_KR_Spec.yaml
  operationId: travelrule-enclave-check-Transaction-Status
hidden: false
---
이 API는 수신 VASP가 송신 VASP로 트랜잭션의 상태 조회를 질의하기 위해 사용되는 API입니다. 수신 VASP측에서 사용자 검증을 완료했음에도 일정 시간 내에 송신 VASP로부터 트랜잭션 결과 Report 또는 Error Report가 없는 경우 모니터링을 위해 전송 결과를 조회하는 경우에 호출할 수 있습니다.

## API 활용 예제

본 API는 검증을 이미 수행한 건에 대한 상태 조회 목적 외에도, 미확인 입금건에 대한 추적 용도로 다음과 같이 활용할 수 있습니다.

1. 확인할 수 없는 입금을 감지한 경우, 수신 VASP는 Lookup Verification Result API를 해당 트랜잭션 hash와 관련된 검증 이력을 조회할 수 있습니다.
2. 해당 트랜잭션과 관련된 검증 이력이 없는 경우, 일정 시간 동안 송신 VASP의 Transaction Report API호출을 기다리며 대기합니다.
3. 대기 시간 이후에도 트랜잭션 보고가 없는 경우, 수신 VASP는 Verification Result Lookup API를 다시 호출하여 후보 검증 건들을 조회할 수 있습니다.
4. 후보 검증 건을 좁히기 위해 감지된 입금 트랜잭션의 출금 주소(from address)와 입금 주소(to address)를 각각 originatorAccountNumber, beneficiaryAccountNumber 필터로 사용합니다.
5. 조회된 검증 후보들에 대해 수신 VASP는 각각 본 Check Transaction Status API를 호출하여 실제 트랜잭션 ID 정보와의 매칭 여부를 확인할 수 있습니다.