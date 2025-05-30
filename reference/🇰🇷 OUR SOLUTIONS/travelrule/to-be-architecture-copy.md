---
title: To-Be Architecture
excerpt: '이 페이지는 TravelRule 연동을 위한 귀사 VASP의 To-Be Architecture와 구현 범위를 안내합니다. '
deprecated: false
hidden: false
metadata:
  robots: index
---
<Image align="center" border={false} caption="Diagram 1. Future-State VASP: Implementation Scope" src="https://files.readme.io/50e08e8212d7e0a2aaf3ca0ec924c3311a70710be8ef6e0273ea8d700c2d6b52-tr_to_be_arct_1.png" width="400px" />

<br />

**출금 프로세스 내 검증 실행**

To function as an Originating VASP, your system must first integrate account and user verification processes into the existing withdrawal workflow. This implementation ensures compliance with best practices outlined in the **Scenarios and Flows** page. Refer to the **Enclave API Reference** for detailed requirements and guidance.

Travel Rule을 준수하는 송신 VASP로 작동하기 위해, 모든 VASP는 수신 계정 및 수신자 신원 검증 시나리오를 출금 프로세스에 반드시 통합해야 합니다.

이 구현은 시나리오 및 플로우 페이지에서 제시한 Best Practice를 준수하기 위함입니다. 세부 연동 요건은 Enclave API Reference를 참조하시기 바랍니다.

**Implementing New APIs**

In addition to enhancing existing workflows, your VASP must implement five new APIs to enable TravelRule integration. The dashed box in Diagram 1 outlines the scope of these API implementations, which are essential for executing compliance logic. For detailed specifications, refer to the **Implementing VASP APIs** section in the integration guide.

<Image align="center" border={false} caption="Diagram 2. Future-State VASP: Final Architecture" src="https://files.readme.io/00c107710ed90f7bd0430933f94e66c07db48f94e255b7db41651086fcda0413-tr_to_be_arct_2.webp" />

Diagram 2 illustrates the Final Architecture of Future-State of VASP with interaction within the entire system. The Enclave Server, installed alongside the VASP Business Backend within the same infrastructure, interacts with the VASP Business Backend through API calls. It also manages Travel Rule records and related data in a dedicated Enclave Database.

* **Grey arrows** represent the interactions required when acting as an Originating VASP.
* **Green arrows** represent the interactions necessary when functioning as a Beneficiary VASP.

This diagram provides a comprehensive overview of your VASP’s TO-BE architecture. Use it as a reference to plan your development scope and align your implementation with the expected workflows.