---
title: To-Be Architecture
excerpt: 이 페이지에서는 VerifyName 2.0 연동을 위한 VASP Backend의 To-Be Architecture와 구현 범위를 안내합니다.
deprecated: false
hidden: false
metadata:
  robots: index
---
## VASP Backend To-Be Architecture

Diagram 1은 VerifyName 프로토콜을 지원하기 위한 VASP Backend의 To-Be 아키텍처를 보여줍니다. VASP의 Travel Rule 규제 의무 여부에 따라 필수 구현해야 하는 API의 범위는 다음과 같이 달라집니다.

<br />

<Image align="center" border={false} caption="Diagram 1. Future-State Overview for Unregulated and Regulated VASPs" src="https://files.readme.io/3d0a215b3227af898e113287fb06d5a6e332bf6aac1e7b91378449d36e25d106-2_0_tobe.png" />

* **Travel Rule 규제 의무가 없는(Travel Rule Non-Obliged) VASP**의 경우 규제 의무 대상 VASP로부터의 검증 요청에 대응하기 위해 Backend에서 VerifyName API와 Callback API를 구현하여 Enclave에게 제공해야 합니다.
* **Travel Rule 규제 의무가 있는(Travel Rule Obliged) VASP**는 자산의 출금 과정에 송/수신자 일치 여부를 검증하기 위한 사전 검증(Pre-Verification) 프로세스를 구현하여 연동해야 합니다. 또한 규제 의무 대상이 아닌 VASP로부터의 입금건이 확인되는 경우 해당 VASP와의 사후 검증(Post-Verification)을 수행해야 합니다.
* **Travel Rule 규제 의무 대상 여부와 상관없이**, VerifyName 2.0 프로토콜을 지원하는 모든 VASP는 응답 가능한 **VerifyName API**를 구현하여 제공해야 할 의무가 있습니다.

<br />

## Overall To-Be Architecture

Diagram 2와 Diagram 3은 각각 Travel Rule 규제 의무가 없는 VASP와 규제 의무가 있는 VASP의 전체 To-Be 아키텍쳐를 보여줍니다. 규제 의무 여부와 상관 없이, VerifyName 2.0 프로토콜을 지원하는 **모든 VASP는 VerifyVASP Enclave를 VASP 인프라 내에 필수적으로 설치하여 연동**해야합니다. VASP Backend에 구현된 VerifyName API와 Callback API를 호출할 수 있는 Endpoint들은 Enclave 설정과 VerifyVASP 콘솔 등록 정보에 포함되어

<Image align="center" border={false} caption="Diagram 2: Future-State for Unregulated VASPs" src="https://files.readme.io/4f92bffd7c1f3756ff1b5c9637a9c84e24da9c8162aa5018169874d4f44192f7-unregulated_1.png" />

<br />

Diagram 2 shows the Future-State architecture for Travel Rule Non-Obliged VASPs, including their connection to the VerifyVASP Central Server:

* **Travel Rule Non-Obliged VASPs** do not need to communicate with the Central Server directly or install the Enclave.
* Instead, they must implement the VerifyName API endpoint and register it via the VerifyVASP console. Verification requests are unidirectional and handled entirely by the VASP’s backend.

<br />

<Image align="center" border={false} caption="Diagram 3: Future-State for Travel Rule Obliged VASPs" src="https://files.readme.io/5ffff93f2bb5be6e91478c055580686e6f178a278da5ad6b25d0355439a35fa7-regulated_1.png" />

Diagram 3 presents the Future-State architecture for Travel Rule Obliged VASPs, including their connection to the VerifyVASP Central Server:

* **Travel Rule Obliged VASPs** must install and configure the Enclave to handle communications with the Central Server.
* Pre-verification is performed by modifying the withdrawal process, ensuring name and date of birth validation before initiating blockchain transactions.

To build architectures similar to those shown in Diagram 2 and Diagram 3, refer to the following detailed integration guides provided for Travel Rule Non-Obliged VASPs and here for Travel Rule Obliged VASPs.

* [Travel Rule Non-Obliged VASP (Provider)]()
* [Travel Rule Obliged VASP]()

<br />

내용 확인 후, 링크 유지 확인 필요