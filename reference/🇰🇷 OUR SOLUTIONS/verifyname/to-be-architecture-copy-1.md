---
title: To-Be Architecture
excerpt: 이 페이지에서는 VerifyName 2.0 연동을 위한 VASP Backend의 To-Be Architecture와 구현 범위를 안내합니다.
deprecated: false
hidden: false
metadata:
  robots: index
---
## VASP Backend To-Be Architecture

Diagram 1은 VerifyName 연동이 완료된 VASP Backend의 To-Be 아키텍처입니다. 각 VASP는 Travel Rule 규제 의무 여부에 따라 다음과 같이 프로세스 연동 및 API 구현을 진행해야 합니다.

#### 입출금 검증 프로세스 통합

Travel Rule 규제 준수 의무가 있는 VASP는 자산의 출금 과정에 송/수신자 일치 여부를 검증하기 위한 사전 검증(Pre-Verification) 프로세스를 구현하여 연동해야 합니다. 또한 규제 의무 대상이 아닌 VASP로부터의 입금건이 확인되는 경우 해당 VASP와의 사후 검증(Post-Verification)을 수행해야 합니다.

<br />

#### 필수 API 구현 (Implementing Required APIs)

VASP는 VerifyName 연동을 위해 Travel Rule 규제 준수 의무 여부에 따라 Diagram 1에 표시된 필수 API들을 백엔드에 구현해야 합니다. 이들 API는 VerifyName 프로토콜을 통한 검증 및 Report 절차를 수행하는 데 필수적입니다. 특히 VerifyName API의 경우 VASP의 규제 준수 의무 여부와 상관없이 반드시 구현되어야합니다.

각 API의 세부 사양은 아래 VerifyName VASP API 문서 목록에서 확인할 수 있습니다.

* [VerifyName API](ref:verifyname-request-verification)
* [Check Transaction Status API](ref:verifyname-transaction)
* [Callback API](ref:verifyname-callback)

<br />

<Image align="center" border={false} caption="Diagram 1. To-Be Backend Architecture of Unregulated and Regulated VASPs" src="https://files.readme.io/3d0a215b3227af898e113287fb06d5a6e332bf6aac1e7b91378449d36e25d106-2_0_tobe.png" />

<br />

## 전체 시스템 To-Be Architecture

Diagram 2와 Diagram 3은 각각 Travel Rule 규제 의무가 없는 VASP와 규제 의무가 있는 VASP의 인프라 전체와 VerifyVASP 중앙 서버를 포함한 전체 To-Be 아키텍처입니다. 규제 의무 여부와 상관 없이, VerifyName 2.0 프로토콜을 지원하는 **모든 VASP는 VerifyVASP Enclave를 VASP 인프라 내에 필수적으로 설치하여 연동**해야합니다. 또한 **Enclave 전용 데이터베이스를 설정**하여 Enclave로부터의 연동 이력 및 관련 데이터를 저장하고 조회할 수 있도록 구성해야 합니다.

* VASP Backend에 구현된 필수 API들은 그 Endpoint를 VerifyVASP 콘솔을 통해 사전에 반드시 등록하여, 중앙 서버에 요청에 따라 Enclave를 통해 호출 가능해야합니다.

<Image align="center" border={false} caption="Diagram 2: To-Be Architecture of Unregulated VASPs" src="https://files.readme.io/4f92bffd7c1f3756ff1b5c9637a9c84e24da9c8162aa5018169874d4f44192f7-unregulated_1.png" />

Diagram 2와 같이 Travel Rule 규제 의무가 없는 VASP들은 주로 중앙서버와 Enclave로부터 검증 요청 및 결과 Report를 받아 처리하는 방식으로 VerifyName 프로토콜을 지원합니다.

<br />

<Image align="center" border={false} caption="Diagram 3: To-Be Architecture of Regulated VASPs" src="https://files.readme.io/9d5a6a5ebeb9f734ef2ea9895e6cac3610777b697d2dbf7ee97717c452ba9de4-regulated_1.png" />

Travel Rule 규제 준수 의무가 있는 VASP는 Diagram 3과 같이 입/출금시 Enclave의 Verification API를 호출하여 사전 또는 사후 검증을 진행해야 합니다. 또한 검증 결과에 따라  입/출금 진행 여부를 확정하는 경우 그 결과를 Enclave의 Report API를 호출하여 상대 VASP에게 공유해야 하는 의무를 갖습니다.