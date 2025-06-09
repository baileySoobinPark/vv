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

VerifyName 프로토콜은 트랜잭션 실행 시점을 기준으로 사전 검증과 사후 검증 시나리오를 모두 지원합니다. 호출 시점에 따른 검증 요청 구현 요구사항이 다음과 같습니다.

**사후 검증(Post Verification)**

* 송신 VASP의 자산 전송 트랜잭션이 실행 된 후, 수신 VASP로부터 검증을 요청하는 경우입니다.
* 요청의 type은 v