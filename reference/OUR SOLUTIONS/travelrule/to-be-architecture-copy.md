---
title: To-Be Architecture
excerpt: '본 문서는 TravelRule 연동을 위한 VASP Backend의 To-Be Architecture와 구현 범위를 안내합니다. '
deprecated: false
hidden: false
metadata:
  robots: index
---
## VASP Backend To-Be Architecture

Diagram 1은 TravelRule 연동이 완료된 VASP 백엔드의 To-Be Architecture입니다. TravelRule 연동을 위해 VASP Business Logic 외부의 점선으로 구분된 영역에 표기된 관련 프로세스를 통합하고 필요한 기능을 구현해야 합니다. 세부 내용은 아래와 같습니다.

<Image align="center" border={false} caption="Diagram 1. Future-State VASP: Implementation Scope" src="https://files.readme.io/50e08e8212d7e0a2aaf3ca0ec924c3311a70710be8ef6e0273ea8d700c2d6b52-tr_to_be_arct_1.png" width="400px" />

<br />

### 출금 검증 프로세스 통합

Travel Rule을 준수하는 송신 VASP로 작동하기 위해, 모든 VASP는 수신 계정 및 수신자 신원 검증 시나리오를 출금 프로세스에 반드시 통합해야 합니다. [Scenarios and Flows](ref:flow-diagram-copy) 섹션에 정의된 Best Practice의 Workflow와 같이 동작함을 보장함으로써 규제 요건을 충족하고 VASP의 역량을 강화할 수 있습니다.

<br />

### 필수 API 구현 (Implementing Required APIs)

VASP는 TravelRule 연동을 위해 Diagram 1에 표기된 4개의 핵심 API와 데이터베이스 관리를 위한 1개의 보조 API를 백엔드에 구현해야 합니다. 이들 API는 검증 및 Report 절차를 수행하는 데 필수적이며, Enclave가 해당 API를 호출할 수 있도록 제공되어야 합니다.

각 API의 세부 사양은 아래 VASP API 문서 목록에서 확인할 수 있습니다.

* [Verify User Account API](ref:travelrule-user-account-verification)
* [Verify User API](ref:travelrule-user-verification)
* [Check Transaction Status API](ref:travelrule-enclave-check-transaction-status)
* [Callback API](ref:travelrule-callback-api)
* [Database Setup](ref:travelrule-database-setup)

<br />

## 전체 시스템 To-Be Architecture

<Image align="center" border={false} caption="Diagram 2. Future-State VASP: Final Architecture" src="https://files.readme.io/00c107710ed90f7bd0430933f94e66c07db48f94e255b7db41651086fcda0413-tr_to_be_arct_2.webp" />

Diagram 2는 VASP의 최종 시스템 아키텍처입니다. 각 VASP는 VASP 백엔드와 동일한 인프라 내에 **Enclave 서버를 설치**하고 VASP 백엔드와 TravelRule 프로세스 수행에 필요한 **API 호출을 주고 받도록 연동**해야 합니다. 또한 **Enclave 전용 데이터베이스를 설정**하여 Enclave로부터 연동 이력 및 관련 데이터를 저장하고 조회할 수 있도록 구성해야 합니다.

* **회색 화살표**는 Ordering VASP로 동작할 때의 연동 흐름을 나타냅니다.
* **녹색 화살표**는 Beneficiary VASP로 동작할 때의 연동 흐름을 나타냅니다.

This diagram provides a comprehensive overview of your VASP’s TO-BE architecture. Use it as a reference to plan your development scope and align your implementation with the expected workflows.