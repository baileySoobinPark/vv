---
title: Check Transaction Status API
api:
  file: VN_Enclave_API_Spec.yaml
  operationId: post_v2-owner-verifications-request-id-tx-inquiry
hidden: false
---
본 API는 송신 VASP로 트랜잭션의 상태를 질의하기 위해 사용되는 API입니다. 수신 VASP측에서 사용자 검증을 완료했음에도 일정 시간 내에 송신 VASP로부터 트랜잭션 결과 Report 또는 Error Report가 없는 경우 자산 이전 결과를 질의하고자 호출할 수 있습니다.

***

## API 명세