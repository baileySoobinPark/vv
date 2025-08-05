---
title: To-Be Architecture
excerpt: >-
  This section describes the To-Be Architecture of a VASP backend for VerifyName
  2.0 integration, along with the required implementation scope.
deprecated: false
hidden: false
metadata:
  robots: index
---
## VASP Backend To-Be Architecture

Diagram 1 shows the To-Be Architecture of a VASP backend after integrating the VerifyName protocol.\
Each VASP must implement the processes and APIs outlined in the diagram according to its Travel Rule regulatory obligation status.

#### Integrating Deposit and Withdrawal Verification Processes

* Travel Rule–obliged VASPs must implement Pre-Verification to verify that the originator and beneficiary are the same person before executing an asset transfer.
* They must also perform Post-Verification for deposits received from non-obliged VASPs to ensure compliance.

<br />

#### Implementing Required APIs

For VerifyName integration, VASPs must implement the APIs indicated in Diagram 1. The following APIs are required for all VASPs, regardless of Travel Rule obligation status. Refer to the VerifyName VASP API documentation for detailed specifications.

* [VerifyName API](ref:verifyname-request-verification)
* [Callback API](ref:verifyname-callback)

<br />

<Image align="center" border={false} caption="Diagram 1. To-Be Backend Architecture of Unregulated and Regulated VASPs" src="https://files.readme.io/785a094359175dbe0cf82c29b8d7636bb432cca76f33477725c7ccaf1a3208ab-future_state_vn.png" />

<br />

## Full System To-Be Architecture

Diagram 2 and Diagram 3 show the complete infrastructure architecture, including the VASP backend, the **VerifyVASP Central Server**, and the **Enclave**.

* All VASPs supporting the VerifyName 2.0 protocol — regardless of Travel Rule obligation status — must install the VerifyVASP Enclave within their infrastructure.
* A dedicated Enclave database must be configured to store and retrieve integration logs and related data from the Enclave.

<br />

<Image align="center" border={false} caption="Diagram 2: To-Be Architecture of Unregulated VASPs" src="https://files.readme.io/09e36f2ab357cb5d7dcfd546ec94d3676502b7aae6c85d84583d6e8fd02d0067-vn_tobe_architecture1.png" />

**Non-obliged VASPs (Diagram 2)**

* Primarily support the VerifyName protocol by receiving verification requests and result reports from the Central Server and Enclave.

<br />

<Image align="center" border={false} caption="Diagram 3: To-Be Architecture of Regulated VASPs" src="https://files.readme.io/1d4058a963ff58389a93ef0a4a15335ba14cb5145c4ceb4ab8a690d73e08b9f3-vn_tobe_architecture2.png" />

<br />

**Travel Rule–obliged VASPs (Diagram 3)**

* Must call the Enclave’s Verification APIs during deposits and withdrawals to perform pre- or post-verification.
* If the decision to proceed with a deposit or withdrawal is based on verification results, the VASP must call the Enclave’s Report APIs to share the outcome with the counterparty VASP.