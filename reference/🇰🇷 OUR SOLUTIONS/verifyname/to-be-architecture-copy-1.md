---
title: To-Be Architecture
excerpt: 이 페이지에서는 VerifyName 2.0 연동을 위한 VASP Backend의 To-Be Architecture와 구현 범위를 안내합니다.
deprecated: false
hidden: false
metadata:
  robots: index
---
## VASP Backend To-Be Architecture

Diagram 1은 VerifyName 연동이 완료된 VASP Backend의 To-Be 아키텍처입니다. VASP의 Travel Rule 규제 의무 여부에 따라 필수 구현해야 하는 API(점선 표시)의 범위는 다음과 같이 달라집니다.

* **Travel Rule 규제 의무가 없는(Travel Rule Non-Obliged) VASP**의 경우 규제 의무 대상 VASP로부터의 검증 요청에 대응하기 위해 Backend에서 VerifyName API와 Callback API를 구현하여 Enclave에게 제공해야 합니다.
* **Travel Rule 규제 의무가 있는(Travel Rule Obliged) VASP**는 자산의 출금 과정에 송/수신자 일치 여부를 검증하기 위한 사전 검증(Pre-Verification) 프로세스를 구현하여 연동해야 합니다. 또한 규제 의무 대상이 아닌 VASP로부터의 입금건이 확인되는 경우 해당 VASP와의 사후 검증(Post-Verification)을 수행해야 합니다. VASP는 이 과정에서 필요한 필수 API들을 모두 구현하여 Enclave에게 제공해야 합니다.
* **Travel Rule 규제 의무 대상 여부와 상관없이**, VerifyName 2.0 프로토콜을 지원하는 모든 VASP는 응답 가능한 **VerifyName API**를 구현하여 제공해야 할 의무가 있습니다.

<br />

<Image align="center" border={false} caption="Diagram 1. To-Be Backend Architecture of Unregulated and Regulated VASPs" src="https://files.readme.io/3d0a215b3227af898e113287fb06d5a6e332bf6aac1e7b91378449d36e25d106-2_0_tobe.png" />

<br />

## 전체 To-Be Architecture

Diagram 2와 Diagram 3은 각각 Travel Rule 규제 의무가 없는 VASP와 규제 의무가 있는 VASP의 인프라 전체와 VerifyVASP 중앙 서버를 포함한 전체 To-Be 아키텍쳐를 보여줍니다.

* 규제 의무 여부와 상관 없이, VerifyName 2.0 프로토콜을 지원하는 **모든 VASP는 VerifyVASP Enclave를 VASP 인프라 내에 필수적으로 설치하여 연동**해야합니다.

<Image align="center" border={false} caption="Diagram 2: To-Be Architecture of Unregulated VASPs" src="https://files.readme.io/4f92bffd7c1f3756ff1b5c9637a9c84e24da9c8162aa5018169874d4f44192f7-unregulated_1.png" />

Diagram 2와 같이 VASP Backend에 구현된 필수 API들은 그 Endpoint를 VerifyVASP 콘솔을 통해 사전에 반드시 등록하여, 중앙 서버 및 Enclave를 통해 호출 될 수 있도록 해야합니다.

<br />

<Image align="center" border={false} caption="Diagram 3: To-Be Architecture of Regulated VASPs" src="https://files.readme.io/9d5a6a5ebeb9f734ef2ea9895e6cac3610777b697d2dbf7ee97717c452ba9de4-regulated_1.png" />

Travel Rule 규제 준수 의무가 있는 VASP는 Diagram 3과 같이 입/출금시 Enclave의 Verification API를 호출하여 사전 또는 사후 검증을 진행해야 합니다. 또한 검증 결과에 따라  입/출금 진행 여부를 확정하는 경우 그 결과를 Enclave의 Report API를 호출하여 상대 VASP에게 공유해야 하는 의무를 갖습니다.