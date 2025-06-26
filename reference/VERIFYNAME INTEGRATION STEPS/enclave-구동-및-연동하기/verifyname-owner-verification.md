---
title: Request Owner Verification API
api:
  file: VN_Enclave_API_Spec.yaml
  operationId: verifyname-owner-verification
hidden: false
---
이 API를 호출하여 요청한 지갑 주소의 소유자 정보와 귀사 VASP가 확보한 사용자 정보를 비교 검증하여 송신자와 수신자의 동일인 여부를 확인할 수 있습니다.

***

동일인 검증(Owner Verification)은 다음 두 가지 시나리오에서 수행됩니다.

* **Post Verification**: 송신 VASP가 출금 트랜잭션을 실행한 후, 수신 VASP의 요청에 의해 송신 지갑 주소의 소유자가 수신인과 동일인인지 검증합니다.
* **Pre Verification**: 출금 트랜잭션 실행 전, 송신 VASP의 요청에 의해 송신인과 수신 지갑 주소의 소유자가 동일인인지 여부를 검증합니다.

응답의 `verification_results` 객체 내 각 검증 항목 별 결과는 각각 MATCHED, MISMATCHED, SKIPPED 중 하나로 반환됩니다.

***

## API 명세