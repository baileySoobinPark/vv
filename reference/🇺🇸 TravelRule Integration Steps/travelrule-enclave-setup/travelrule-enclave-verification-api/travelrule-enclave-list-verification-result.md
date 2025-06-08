---
title: List Verification Result API
api:
  file: TR_Enclave_API_Spec.yaml
  operationId: travelrule-enclave-list-Verification-Result
hidden: false
---
쿼리 조건을 만족하는 검증 결과 이력 목록을 조회할 수 있는 API입니다. 다양한 필터 조건(쿼리 파라미터)을 지원하며, 검증 상태, 기간, 자산 종류, 상대 VASP등 지정한 조건을 부합하는 검증 결과만 응답으로 반환됩니다.

모든 조건은 선택 필드로서 복수 조건 사용시 AND 조건으로 적용됩니다.

***