---
title: List Verification Result API
api:
  file: TR_Enclave_API_Spec.yaml
  operationId: travelrule-enclave-list-Verification-Result
hidden: false
---
쿼리 조건에 따라 검증 결과 이력 목록을 조회할 수 있는 API입니다. 검증 상태, 기간, 자산 종류, 상대 VASP 등 다양한 쿼리 파라미터(필터 조건)을 지원하며, 지정한 조건을 만족하는 항목만 응답으로 반환됩니다.

모든 조건은 선택 필드로서 복수 조건 사용시 AND 조건으로 적용됩니다.

***

## API 명세