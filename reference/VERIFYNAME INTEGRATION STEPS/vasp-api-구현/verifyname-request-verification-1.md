---
title: VerifyName API
api:
  file: VN_VASP_API_KR_Spec.yaml
  operationId: verifyName-Request-Verification
hidden: false
---
수신 계정의 소유자가 송신자와 동일인인지 여부를 검증하는 VerifyName의 핵심 검증 과정에서 Enclave에 의해 호출되는 API입니다. 요청에 포함된 전송 자산 정보와 수신 주소의 유효성을 검증하고, 해당 주소 소유자의 성명과 생년월일을 응답으로 반환합니다. 반환된 개인 정보는 Enclave 내부에서 송신자 정보와의 비교 검증에 사용됩니다. VerifyName을 지원하는 모든 VASP는 반드시 이 API를 구현하여 타 VASP들로부터의 소유자 검증 요청에 대응해야 합니다.

***

## 구현 가이드

### 기능 요구사항

#### 1. 검증 요청 처리

VerifyName 프로토콜은 트랜잭션 실행 시점을 기준으로 사전 검증과 사후 검증 시나리오를 모두 지원합니다. 시나리오별 검증 요청 처리에 대한 구현 요구사항은 아래과 같으며, VASP는 두 시나리오를 모두 지원해야 합니다.

**사후 검증(Post-Verification) 구현 요구사항**

* 요청 type이 `VerifyOriginator`인 경우로, 귀사의 VASP는 송신 VASP로서 검증 요청에 대응해야 합니다. 송신 VASP로부터 자산 전송 트랜잭션이 먼저 실행되어, 수신 VASP가 검증을 요청한 경우입니다.
* 요청에 포함된 tx\_hash 값이 귀사의 VASP가 실행한 트랜잭션이 맞는지 검증하고, 결과를 `verification_results` 응답의 `tx_hash` 필드로 반환해야 합니다.
* 해당 트랜잭션의 수신 주소가 API 요청에 포함된 `supplementary_data.envelope.address`의 주소와 일치하는지 검증하고, 결과를 `verification_results` 응답의 `address` 필드로 반환해야 합니다.
* <br />

**사전 검증(Pre-Verification) 구현 요구사항**

* 요청 type이 `VerifyBeneficiary`인 경우로, 귀사의 VASP는 수신 VASP로서 검증 요청에 대응해야 합니다. 송신 VASP가 자산 전송을 실행하기에 앞서 수신 VASP로 검증을 요청한 경우입니다.
* 요청의 `supplementary_data.envelope.address`의 주소가 귀사의 VASP에 등록된 수취인 주소 중 하나와 일치하는지 확인해야 합니다.
* <br />