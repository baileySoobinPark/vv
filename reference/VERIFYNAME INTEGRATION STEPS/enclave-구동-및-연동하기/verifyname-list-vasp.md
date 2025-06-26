---
title: VASP List API
api:
  file: VN_Enclave_API_Spec.yaml
  operationId: verifyName-List-VASP
hidden: false
---
자산 이전이 가능한 모든 VASP 목록을 조회하기 위한 API입니다. 고객의 자산 이전 요청 시 본 API를 호출하여 고객이 목록 중 하나의 VASP를 선택하도록 한 뒤, 해당 VASP의 vaspId 값을 활용하여 검증을 요청할 수 있습니다.

***

## VASP 목록 포함 기준

응답의 VASP 목록은 귀사의 VASP를 포함하여 다음 조건을 충족하는 다른 VASP들을 포함합니다.

* 해당 VASP가 VerifyVASP 또는 타 Travel Rule 프로토콜의 회원일 것
* 자산 이전 처리가 가능할 것

***

## API 명세