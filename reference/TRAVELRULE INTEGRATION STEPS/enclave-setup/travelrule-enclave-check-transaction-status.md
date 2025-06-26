---
title: Check Transaction Status API
api:
  file: TR_Enclave_API_Spec.yaml
  operationId: travelrule-enclave-check-Transaction-Status
hidden: false
---
이 API는 수신 VASP가 송신 VASP로 트랜잭션의 상태를 질의하기 위해 사용되는 API입니다. 수신 VASP가 사용자 검증을 완료했음에도 일정 시간 내에 송신 VASP로부터 트랜잭션 결과 리포트(Transaction Report) 또는 에러리포트(Error Report)를 수신하지 못한 경우, 결과를 모니터링하기 위한 용도로 호출할 수 있습니다.

## API 활용 예제

본 API는 기존에 검증이 수행된 건의 상태를 조회하는 용도 외, 미확인 입금건에 대한 추적 절차에서도 다음과 같이 활용할 수 있습니다.

1. 수신 VASP가 확인되지 않는 입금 건을 감지한 경우, Lookup Verification Result API를 통해 특정 트랜잭션 해시와 관련된 검증 이력을 조회할 수 있습니다.
2. 해당 트랜잭션과 관련된 검증 이력이 없는 경우, 일정 시간 동안 송신 VASP의 Transaction Report API호출을 기다리며 대기합니다.
3. 대기 시간 이후에도 트랜잭션 리포트가 없는 경우, 수신 VASP는 Verification Result Lookup API를 다시 호출하여 후보 검증 건들을 조회할 수 있습니다.
4. 후보 범위를 좁히기 위해 감지된 입금 트랜잭션의 입금 주소(beneficiaryAccountNumber)와 자산의 수량(amount), 자산의 종류(symbol) 등을 필터로 지정하여 조회합니다.
5. 검증 후보 건들에 대해, 수신 VASP는 Check Transaction Status API를 각각 호출하여 실제 트랜잭션 해시와의 매칭 여부를 확인합니다.

***

## API 명세