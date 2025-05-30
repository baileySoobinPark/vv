---
title: To-Be Architecture
excerpt: '이 페이지는 TravelRule 연동을 위한 귀사 VASP Backend의 To-Be Architecture와 구현 범위를 안내합니다. '
deprecated: false
hidden: false
metadata:
  robots: index
---
<Image align="center" border={false} caption="Diagram 1. Future-State VASP: Implementation Scope" src="https://files.readme.io/50e08e8212d7e0a2aaf3ca0ec924c3311a70710be8ef6e0273ea8d700c2d6b52-tr_to_be_arct_1.png" width="400px" />

Diagram 1은 TravelRule 연동을 완료한 VASP 백엔드의 To-Be Architecture 입니다. TravelRule 연동은 `VASP Business Logic`으로 표시된 부분 외 영역의 점선으로 표시된 영역에 대한 프로세스 통합 및 구현을 필수로 요구합니다.

<br />

### 출금 검증 프로세스 통합

Travel Rule을 준수하는 송신 VASP로 작동하기 위해, 모든 VASP는 수신 계정 및 수신자 신원 검증 시나리오를 출금 프로세스에 반드시 통합해야 합니다. [Scenarios and Flows](ref:flow-diagram-copy) 섹션에 정의된 Best Practice의 Workflow와 같이 동작함을 보장함으로써 규제 요건을 충족하고 VASP의 역량을 강화할 수 있습니다.

<br />

### 필수 API 구현 (Implementing Required APIs)

VASP는 TravelRule 연동을 위해 Diagram 1에 표기된 4개의 필수 API와 Database 관리를 위한 1개 추가 API를 백엔드 내부에 구현해야 합니다. 이들 API는 검증 및 Report 프로세스를 처리하기 위한 필수 요구사항으로서 반드시 구현되어 Enclave로부터 호출 될 수 있어야 합니다. 각 API에 대한 자세한 사양은 아래 API 문서 링크를 참고하세요.

* [Verify User Account API](ref:travelrule-user-account-verification)
* [Verify User API](ref:travelrule-user-verification)
* [Check Transaction Status API](ref:travelrule-enclave-check-transaction-status)
* [Callback API](ref:travelrule-callback-api)
* [Database Setup](ref:travelrule-database-setup)

TravelRule 연동을 위해 총 5개 신규 API 구현이 요구됩니다. 해당 API 목록은 Diagram 1에 요약되어 있으며, 검증 및 보고 로직 수행에 필수적입니다.\
자세한 사양은 Implementing VASP APIs 섹션을 참조하세요.

In addition to enhancing existing workflows, your VASP must implement five new APIs to enable TravelRule integration. The dashed box in Diagram 1 outlines the scope of these API implementations, which are essential for executing compliance logic. For detailed specifications, refer to the **Implementing VASP APIs** section in the integration guide.

<Image align="center" border={false} caption="Diagram 2. Future-State VASP: Final Architecture" src="https://files.readme.io/00c107710ed90f7bd0430933f94e66c07db48f94e255b7db41651086fcda0413-tr_to_be_arct_2.webp" />

Diagram 2 illustrates the Final Architecture of Future-State of VASP with interaction within the entire system. The Enclave Server, installed alongside the VASP Business Backend within the same infrastructure, interacts with the VASP Business Backend through API calls. It also manages Travel Rule records and related data in a dedicated Enclave Database.

* **Grey arrows** represent the interactions required when acting as an Originating VASP.
* **Green arrows** represent the interactions necessary when functioning as a Beneficiary VASP.

This diagram provides a comprehensive overview of your VASP’s TO-BE architecture. Use it as a reference to plan your development scope and align your implementation with the expected workflows.