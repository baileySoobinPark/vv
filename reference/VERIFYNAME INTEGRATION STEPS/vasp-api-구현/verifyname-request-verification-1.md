---
title: VerifyName API
api:
  file: VN_VASP_API_KR_Spec.yaml
  operationId: verifyName-Request-Verification
hidden: false
---
수신 계정의 소유자가 송신자와 동일인인지 여부를 검증하고 결과를 반환하는 API입니다. 이는 VerifyName 프로토콜의 핵심 기능으로서 VerifyName을 지원하는 모든 VASP는 이 API를 필수로 구현하여 다른 VASP들로부터의 검증 요청에 대응해야 합니다.

***

## 구현 가이드

### 기능 요구사항

#### 1. 검증 요청 처리