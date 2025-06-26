---
title: Risk Assessment Via Chainalysis Sanction API
api:
  file: TR_Enclave_API_Spec.yaml
  operationId: travelrule-Chainalysis-Sanction
hidden: false
---
이 API는 Chainalysis Sanction API를 활용하여 지갑 주소의 리스크를 사전에 평가하는데 사용됩니다. 응답 결과를 바탕으로, VASP는 특정 주소의 잠재적 위험을 식별하고 비인가 또는 고위험 거래를 사전에 차단할 수 있습니다.

***

## Chainalysis Sanction API란?

[Chainalysis](https://www.chainalysis.com/)에서 무료로 제공하는 API로, 가상자산 지갑 주소의 제재 여부 및 위험 수준을 평가하는데 사용됩니다. VASP를 포함한 기관은 이를 통해 제재 대상 주소나 규제상 문제가 있는 주소를 신속하게 식별할 수 있습니다. 해당 API는 자금세탁방지(AML) 및 제재 스크리닝 요구사항을 보다 효율적으로 충족하도록 지원하며, 가상자산 거래의 보안성과 신뢰성을 높입니다.

* API 사용을 위해서는 먼저 Chainalysis [Sign-Up 링크](https://go.chainalysis.com/crypto-sanctions-screening.html)를 통해 API 키를 발급받아야 합니다.
* 사용 가이드 등 상세한 정보는 [공식 문서](https://public.chainalysis.com/docs/index.html)를 참조하십시오.

## 구현 가이드

송신 VASP와 수신 VASP 모두 Sanction API를 활용해 사용자 계정의 추가 리스크 평가를 수행할 수 있으며,이는 내부 규제 요건 수립 시 활용될 수 있습니다. 단, Sanction API 호출 전 반드시 사용자 검증 API(POST /verifications)이 선행되어야 합니다. Sanction API 구현 가이드는 다음과 같습니다.

* 송신 VASP: 사용자 검증 이후 자산 이전을 수행하기 전에 수신자의 지갑 주소에 대한 리스크를 평가하고 내부 정책에 따라 자산 이전 여부를 판단할 수 있습니다.
* 수신 VASP: 송신자의 지갑 주소를 대상으로 리스크 평가를 수행하여 잠재적인 위협 요소를 식별하고, 필요한 경우 사전 조치를 취할 수 있습니다.

## 사용 전 준비 사항

1. **Enclave 환경 변수 설정**\
   Enclave 구동 전, 등록 과정에서 발급받은 API 키를 `VEGA_CHAINALYSIS_SANCTION_API_KEY` 환경 변수에 반드시 설정해야 합니다.
2. **데이터베이스 테이블 구성**\
   Enclave 서버가 Chainalysis Sanction API 결과를 저장할 수 있도록 데이터베이스 내에 전용 테이블을 구성해야 합니다. 구현 세부사항은 [Enclave 데이터베이스 생성](ref:database-setup-copy) 페이지의 Chainalysis 관련 테이블 정의를 참조하세요.

## API 명세