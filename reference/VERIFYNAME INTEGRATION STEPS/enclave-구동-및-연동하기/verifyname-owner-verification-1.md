---
title: Request Owner Verification API
excerpt: >
  VASPs can initiate the verification flow by calling this API to verify both
  the Originator and the Beneficiary.


  Owner verification is performed in the following two scenarios:
    1. **Post Verification**: The Beneficiary VASP performs Owner Verification after the Originating VASP executes the withdrawal transaction. This process is initiated by the Beneficiary VASP to verify whether the Originator and the Beneficiary represent the same entity.
    2. **Pre Verification**: Before executing the withdrawal transaction, the Originating VASP performs Owner Verification to confirm that the Beneficiary and the Originator represent the same entity.

  ### Owner Verification Result

  - The verification results for the following items — `ticker`, `network`,
  `address`, `tx_hash`, `dti`, `name`, `birth_date`, `date_of_incorporation`,
  and `organisation_identification` — can be returned as one of the following
  values: 
    - `MATCHED`
    - `MISMATCHED`
    - `SKIPPED`
api:
  file: VN_Enclave_API_KR_Spec.yaml
  operationId: verifyname-owner-verification
hidden: false
---
이 API를 호출하여 요청한 주소의 소유주 정보와 귀사 VASP가 확보한 사용자 정보를 비교 검증하여 송신자와 수신자의 동일인인지 여부를 확인할 수 있습니다.

***

소유자 검증(Owner Verification)은 다음 두 가지 시나리오에서 수행됩니다.

* **Post Verification**: 송신 VASP가 출금 트랜잭션을 실행한 후, 수신 VASP의 요청에 의해 송신 주소의 소유자가 수신자가 동일인인지 검증합니다.
* **Pre Verification**: 출금 트랜잭션 실행 전, 송신 VASP의 요청에 의해 송신자와 수신 주소의 소유자가 동일인인지 여부를 검증합니다.

응답의 `verification_results` 객체 내 각 항목에 대한 검증 결과는 각각 MATCHED, MISMATCHED, SKIPPED 중 하나로 반환됩니다.