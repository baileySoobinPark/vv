---
title: To-Be Architecture
excerpt: >-
  This page provides an overview of the implementation scope required for your
  VASP to integrate with the VerifyName protocol. Depending on whether your VASP
  acts as a **Travel-Rule Obliged VASP** performing pre-verification or as an
  **Travel Rule Non-Obliged VASP** (VerifyName Provider) supporting name and
  date of birth verification only upon Beneficiary VASP requests, your
  Future-State architecture will vary. Use the information on this page to
  estimate implementation tasks and ensure a smooth integration process.
deprecated: false
hidden: true
metadata:
  robots: index
---
<Image align="center" border={false} caption="Diagram 1. Future-State Overview for Unregulated and Regulated VASPs" src="https://files.readme.io/4674e99a0ef8971a44c143865ae712663183d880883753d0ed0b16046fd2f370-vn_to_be_arct1.avif" />

Diagram 1 illustrates the Future-State architectures for both Unregulated and Regulated VASPs:

* **Travel Rule Non-Obliged(Unregulated) VASPs**: These VASPs should implement the VerifyName API to facilitate name and date of birth verification requested from Regulated VASPs during virtual asset transfers. No pre-verification or modification to the withdrawal process is required.
* **Travel Rule Obliged(Regulated) VASPs**: These VASPs modify their withdrawal processes to perform **Pre-verification** as the Originator VASP. They do not need to implement or expose the VerifyName API unless acting as a provider.

<br />

<Image align="center" border={false} caption="Diagram 2: Future-State for Unregulated VASPs" src="https://files.readme.io/b61aff040cc9e709ec7e8d8620fabe1d9fdba77ddfcb84f806bf9b5749cc50df-vn_to_be_arct2.png" />

<br />

Diagram 2 shows the Future-State architecture for Travel Rule Non-Obliged VASPs, including their connection to the VerifyVASP Central Server:

* **Travel Rule Non-Obliged VASPs** do not need to communicate with the Central Server directly or install the Enclave.
* Instead, they must implement the VerifyName API endpoint and register it via the VerifyVASP console. Verification requests are unidirectional and handled entirely by the VASP’s backend.

<br />

<Image align="center" border={false} caption="Diagram 3: Future-State for Travel Rule Obliged VASPs" src="https://files.readme.io/71264c80da8be39b01b4e615f5ecadbd13fbf3cb998d1282dd7c22c3577d21c4-vn_to_be_arct3.avif" />

Diagram 3 presents the Future-State architecture for Travel Rule Obliged VASPs, including their connection to the VerifyVASP Central Server:

* **Travel Rule Obliged VASPs** must install and configure the Enclave to handle communications with the Central Server.
* Pre-verification is performed by modifying the withdrawal process, ensuring name and date of birth validation before initiating blockchain transactions.

To build architectures similar to those shown in Diagram 2 and Diagram 3, refer to the following detailed integration guides provided for Travel Rule Non-Obliged VASPs and here for Travel Rule Obliged VASPs.

* [Travel Rule Non-Obliged VASP (Provider)]()
* [Travel Rule Obliged VASP]()