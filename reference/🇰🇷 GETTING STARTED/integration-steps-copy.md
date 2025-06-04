---
title: Integration Steps
excerpt: TravelRule 또는 VerifyName 지원을 위한 주요 연동 단계를 확인하고 연동을 시작하십시오.
deprecated: false
hidden: false
metadata:
  robots: index
---
VerifyVASP 솔루션 연동은 총 6단계로 진행되며, Onboarding을 제외한 단계는 각 프로토콜(TravelRule 또는 VerifyName) 카테고리 에서 세부 가이드가 제공됩니다.

1. **Onboarding**\
   VerifyVASP 콘솔에 VASP를 등록하고 API Key를 발급받습니다.
2. **VASP API 구현**\
   각 프로토콜 지원을 위해 필요한 VASP API를 요구사항에 따라 백엔드에 구현합니다.
3. **Setting up a Database**\
   Enclave와 연동하여 검증 결과 및 사용자 데이터를 안전하게 저장하기 위한 데이터베이스를 구성합니다.
4. **Connecting with Enclave**\
   VerifyVASP에서 제공하는 Enclave Docker를 다운로드 받아 VASP 인프라 내에 설치하고, 중앙 서버 및 VASP 백엔드와 통신할 수 있도록 연동합니다.
5. **Testing with Robot VASP**\
   로봇 VASP를 상대 VASP로 하여 모의 입출금 검증 테스트를 진행합니다. 구현한 VASP API의 기능을 검증하고 Enclave, Enclave 데이터베이스,  VerifyVASP 중앙서버와의 정상 연동 여부를 확인할 수 있습니다.
6. **Maintenance**\
   구현 및 연동이 완료되었다면 프로토콜을 실 서비스에 적용합니다. 안정적인 서비스를 위한 업데이트 및 유지보수 계획을 수립하고 VerifyVASP의 운영 가이드에 따라 운영합니다.