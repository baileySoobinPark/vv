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

WCO API를 통해, 송신 VASP와 수신 VASP는 송신자 또는 수신자의 실명정보를 기반으로 한 리스크 평가를 진행하고 규제 요건으로 활용할 수 있습니다. 단, API 호출 전 반드시 사용자 검증(POST /verifications API 호출)이 완료되어야 합니다.

## 비동기 API

WCO API는 비동기 방식으로 동작하며, 검증 결과는 Callback API를 통해 전달됩니다.

## 사용 전 준비 사항

1. **Enclave 환경 변수 설정**

* `VEGA_REFINITIV_WCO_API_KEY`: Refinitiv 콘솔에서 발급받은 API Key.
* `VEGA_REFINITIV_WCO_API_SECRET`: Refinitiv 콘솔에서 발급받은 API Secret.
* `VEGA_REFINITIV_WCO_GROUP_ID`: 케이스를 분류하기 위한 Group ID. Group은 Refinitiv 콘솔의 어드민 페이지에서 생성 가능하며 Group ID는 Refinitiv WCO API Quick Start Postman Collection의 Group Information > Get my top-level groups 요청을 실행하여 조회할 수 있습니다.

2. **데이터베이스 테이블 구성**\
   Enclave DB에는 WCO API 결과를 저장하기 위한 전용 테이블이 구성되어야 합니다. [Enclave 데이터베이스 생성](ref:database-setup-copy)페이지를 참고하여 적절한 선택 테이블을 생성하십시오.