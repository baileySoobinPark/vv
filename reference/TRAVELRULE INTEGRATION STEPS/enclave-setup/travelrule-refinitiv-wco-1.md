---
title: Risk Assessment Via Refinitiv WCO API
api:
  file: TR_Enclave_API_KR_Spec.yaml
  operationId: travelrule-Refinitiv-WCO
hidden: false
---
이 API는 Refinitiv WCO(World-Check One) API를 활용한 지갑 주소 소유자 및 거래 당사자의 리스크 평가 용도로 사용됩니다. VASP는 잠재적 위험을 사전에 파악함으로서 고위험 거래를 방지할 수 있습니다.

***

## Refinitiv WCO API란?

Refinitiv World-Check One(WCO) API는 개인 또는 법인에 대한 식별 정보를 기반으로 리스크 평가를 수행하는 유료 서비스입니다. 평가 요청은 “케이스(case)“로 생성되며, 각각은 Refinitiv에서 발급하는 caseSystemId로 식별됩니다. 또한, 각 케이스는 하나의 “그룹(group)“에 속하며, 그룹 단위로 케이스를 관리합니다. WCO API 연동 전에 그룹 구성을 미리 검토하고 체계화하는 것이 좋습니다.

* WCO API 사용을 위해서는 Refinitiv 계정 생성과 라이선스 구매가 필요합니다.\
  [Refinitiv WCO 공식 페이지](https://www.refinitiv.com/ko/products/world-check-kyc-screening/world-check-one-kyc-verification) 또는 VerifyVASP팀에 지원을 요청해 미팅을 주선받을 수 있습니다.
* 라이선스를 구매한 후, [WCO 콘솔 사이트](https://worldcheck.refinitiv.com/)에 로그인하여 API Key를 확인할 수 있습니다. Refinitiv WCO API의 자세한 사용법은 [WCO API 개요](https://developers.lseg.com/en/api-catalog/customer-and-third-party-screening/world-check-one-api) 및 [WCO API Reference](https://developers.lseg.com/content/dam/devportal/en_us/product-docs/wc1-api/index.html) 문서를 확인하세요.

## 구현 가이드

송신 VASP와 수신 VASP 모두 WCO API로 송신자 또는 수신자의 리스크 평가를 진행한 뒤 규제 요건으로 활용할 수 있습니다. 단, API 호출 전 반드시 사용자 검증(POST /verifications API 호출)이 완료되어야 하며 VASP의 포지션에 따라 API 호출 시점은 상이할 수 있습니다.

WCO API 호출 전, 반드시 사용자 검증 API(POST /verifications)를 먼저 완료해야 합니다.\
Refinitiv WCO API를 통해 송신자 또는 수신자의 실명 정보를 기반으로 고위험 인물 또는 단체 여부를 평가할 수 있습니다.
•	송신 VASP 또는 수신 VASP는 상대방의 실명 정보 기반으로 위험 여부를 검토할 수 있습니다.
•	WCO API는 비동기 방식으로 동작하며, 검증 결과는 Callback API를 통해 전달됩니다.

⸻

사용 전 준비 사항

1. Enclave 환경 변수 설정\
   •	VEGA\_REFINITIV\_WCO\_API\_KEY: Refinitiv 콘솔 사이트에서 발급받은 API Key
   (경로: Admin Page > Users > \[본인 사용자])
   •	VEGA\_REFINITIV\_WCO\_API\_SECRET: 위와 동일한 위치에서 확인 가능한 API Secret
   •	VEGA\_REFINITIV\_WCO\_GROUP\_ID: 케이스를 분류하기 위한 Group ID
   그룹은 콘솔 사이트의 Admin Page에서 생성 가능
   ✅ Group ID 조회 방법:
   •	Refinitiv WCO API Quick Start Postman Collection에서
   Group Information > Get my top-level groups 요청 실행
   •	이 요청 전, Postman 환경 변수에 API Key와 Secret을 설정해야 합니다
2. 데이터베이스 테이블 구성\
   •	Refinitiv WCO API의 호출 결과는 Enclave DB에 저장됩니다.
   •	별도의 테이블을 구성해야 하며, 테이블 명세는 Refinitiv WCO 결과 테이블 정의서를 참고하세요.