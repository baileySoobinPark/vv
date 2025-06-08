---
title: Risk Assessment Via Chainalysis KYT API
api:
  file: TR_Enclave_API_KR_Spec.yaml
  operationId: travelrule-Chainalysis-KYT
hidden: false
---
이 API는 Chainalysis KYT API를 활용한 지갑 주소 및 트랜잭션의 리스크 평가 용도로 사용됩니다. VASP는 특정 주소와 트랜잭션의 잠재적 위험을 사전에 파악함으로서 비인가 또는 고위험 거래를 방지할 수 있습니다.

***

## Chainalysis KYT API란?

Chainalysis의 Know Your Transaction(KYT) API는 유료 서비스로, 가상자산 거래에 대해 고도화된 리스크 평가 기능을 제공합니다. 이 API는 무료로 제공되는 Sanction API보다 더 정교하고 정확한 리스크 분석을 제공하며, 특히 사전 수신 주소 리스크 평가 및 전송 이후 트랜잭션 위험도 평가에 최적화되어있습니다.

* KYT API 사용을 위해서는 [Chainalysis KYT 소개 페이지](https://www.chainalysis.com/solution/crypto-compliance/)에서 라이선스 구매를 신청해야 합니다.
* 라이선스 구매 후, [KYT 콘솔 사이트](https://kyt.chainalysis.com/)에 로그인하여 API 키를 발급받을 수 있습니다. 자세한 기능 및 구현 방법은 [API Reference 문서](https://docs.chainalysis.com/api/kyt/) 및 [공식 개발자 가이드 문서](https://docs.chainalysis.com/api/kyt/guides/#developer-portal)를 참고하십시오.

## 구현 가이

KYT API 호출 전, 반드시 사용자 검증 API (POST /verifications)이 완료되어야 합니다. 트랜잭션 실행 시점을 기준으로, 다음과 같은 용도로 활용할 수 있습니다.

트랜잭션 리포트 전

1. 송신 VASP\
   •	수신자의 지갑 주소에 대한 리스크를 평가합니다.
   •	출금 전, 수신 주소의 위험도를 사전에 파악할 수 있습니다.
2. 수신 VASP\
   •	이 시점에서는 KYT API 사용이 제한됩니다.
   •	호출 시 UNSUPPORTED-RISK-ASSESSMENT 오류가 반환됩니다.

트랜잭션 리포트 후

1. 송신 VASP\
   •	출금된 트랜잭션의 리스크를 평가합니다.
   •	전송된 트랜잭션에 내재된 위험 요소를 확인할 수 있습니다.
2. 수신 VASP\
   •	입금된 트랜잭션의 리스크를 평가합니다.
   •	입금 트랜잭션에 대한 위험도를 분석하고 이에 따른 조치를 취할 수 있습니다.

KYT API를 통해 송신자는 출금 전 수신 지갑의 리스크를 사전에 평가할 수 있으며, 수신자는 입금 후 트랜잭션의 리스크를 평가하여 컴플라이언스 및 보안 조치를 수행할 수 있습니다.

⸻

비동기 동작 방식

KYT API는 비동기 방식으로 동작합니다.\
송신 또는 수신 트랜잭션을 Chainalysis에 등록하면, 평가 결과는 Enclave 서버의 Callback API를 통해 전달됩니다.

⸻

사용 전 준비 사항

1. Enclave 환경 변수 설정\
   •	발급받은 API 키를 VEGA\_CHAINALYSIS\_KYT\_API\_KEY 환경 변수에 설정해야 합니다.
2. 데이터베이스 테이블 구성\
   •	Enclave DB에는 Chainalysis KYT API 결과를 저장하기 위한 전용 테이블이 구성되어야 합니다.
   •	구현을 위해 아래 테이블 정의를 참고하세요:
   •	Chainalysis KYT 결과 테이블
   •	Chainalysis KYT 알림 테이블