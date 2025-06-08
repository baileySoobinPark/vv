---
title: Risk Assessment Via Chainalysis Sanction API
api:
  file: TR_Enclave_API_KR_Spec.yaml
  operationId: travelrule-Chainalysis-Sanction
hidden: false
---
이 API는 Chainalysis Sanction API를 활용한 지갑 주소의 리스크 평가 용도로 사용됩니. 응답 결과에 따라, VASP는 특정 주소의 잠재적 위험을 사전에 파악함으로서 비인가 또는 고위험 거래를 방지할 수 있습니다.

***

## Chainalysis Sanction API란?

[Chainalysis](https://www.chainalysis.com/)에서 무료로 제공하는 공개 API로, 가상자산 지갑 주소의 제재 여부 및 위험 수준을 평가합니다. VASP를 포함한 기관은 이 API를 통해 제재 대상 주소나 규제상 문제가 있는 주소를 신속하게 식별할 수 있습니다. 해당 API는 자금세탁방지(AML) 및 제재 스크리닝 요구사항을 보다 효율적으로 충족하도록 지원하여, 가상자산 거래의 보안성과 신뢰성을 높입니다.

* API 사용을 위해서는 먼저 Chainalysis [Sign-Up 링크](https://go.chainalysis.com/crypto-sanctions-screening.html)를 통해 API 키를 발급받아야 합니다.
* 사용 가이드 등 상세한 정보는 [공식 문서](https://public.chainalysis.com/docs/index.html)를 참조하십시오.

## 구현 가이드

송신 VASP와 수신 VASP 모두 Sanction API를 통한 사용자 계정의 추가 리스크 평가를 진행하여 규제 충족 요건으로 활용할 수 있습니다. 단, Sanction API 호출 전 반드시 사용자 검증 API(POST /verifications)가 먼저 호출되어야 합니다. 구현 가이드는 다음과 같습니다.

* 송신 VASP의 경우, 사용자 검증 이후 자산 전송을 수행하기 전에 수신자의 지갑 주소에 대한 리스크를 평가하여 자체 규제 요건으로 활용할 수 있습니다.
* 수신 VASP의 경우, 송신자의 지갑 주소에 대해 리스크 평가를 수행하여 잠재적인 위협 요소를 식별하고 사전 조치를 취할 수 있습니다.

## 사용 전 준비 사항

1. **Enclave 환경 변수 설정**\
   Enclave 구동 전, 등록 과정에서 발급받은 API 키를 `VEGA_CHAINALYSIS_SANCTION_API_KEY` 환경 변수에 반드시 설정해야 합니다.
2. **데이터베이스 테이블 구성**\
   Enclave 서버가 Chainalysis Sanction API 결과를 저장할 수 있도록 데이터베이스 내에 전용 테이블을 구성해야 합니다. 구현 세부사항은 [Enclave 데이터베이스 생성](ref:database-setup-copy) 페이지의 Chainalysis 관련 테이블 정의를 참조하세요.

## API 명