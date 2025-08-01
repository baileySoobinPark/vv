---
title: Overview
excerpt: >-
  This document provides an overview of VerifyVASP’s architecture and the
  verification processes based on the TravelRule and VerifyName protocols.
  Before beginning integration, review the available security options and the
  integration sequence on the VASP side to plan your implementation.
deprecated: false
hidden: false
metadata:
  robots: index
next:
  description: Flow Diagram을 통해 입출금 시나리오에서의 요청 순서와 통신 흐름을 확인하고 TravelRule 프로토콜을 이해할 수 있습니다.
---
## Architecture Overview

Diagram 1 illustrates the components of the VerifyVASP service and the communication flow between them. Key features include:

<Image align="center" border={false} caption="Diagram 1. VerifyVASP Solution Architecture Overview" src="https://files.readme.io/8d27021ec8f83d7f4cc31b17bccc04e96360c65217d142e4733739024c89930b-tr_solution_1.png" />

<br />

#### Communication via VerifyVASP Central Server

* All TravelRule and VerifyName communications between VASPs are relayed through the **VerifyVASP Central Server**.
* Depending on the transaction scenario, each VASP may act as either the **Ordering VASP** (originator’s VASP) or the **Beneficiary VASP** (beneficiary’s VASP).
* All requests and responses are routed through the Central Server.

<br />

#### Enclave Installation and Integration

**Enclave** is a pre-built server module that provides a communication interface with the VerifyVASP Central Server.

* Distributed as a **Docker image**, the Enclave server and its dedicated database must be installed within each VASP’s infrastructure.
* The VASP backend communicates with the Enclave server via API calls and **never communicates directly** with the VerifyVASP Central Server API.

<br />

#### Data Security and Privacy

* The Enclave’s dedicated database is accessible **only** by the Enclave server, ensuring data integrity and isolation.
* All communications between VASPs and the Central Server use **HTTPS**, and all sensitive information is **end-to-end encrypted (E2EE)**.

***

<br />

## Verification Overview

<br />

### TravelRule Verification Process

The TravelRule verification process ensures that the beneficiary and their account are validated before asset transfers.\
The process typically involves six steps, as outlined below. Detailed API specifications and integration flows can be found in the Scenarios and Flows and Integration Guide documents.

<HTMLBlock>{`
<!DOCTYPE html>

<html lang="ko">
<head>
  <meta charset="UTF-8">
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background-color: #f5f8fb;
      padding: 40px;
      max-width: 1000px;
      margin: auto;
    }


.flow-container {
  display: flex;
  flex-direction: column;
  gap: 40px;
}

.row {
  display: flex;
  flex-wrap: nowrap;
  justify-content: space-between;
  align-items: stretch;
  gap: 12px;
}

.step-box {
  background-color: #ffffff;
  border: 1px solid #1364FF;
  border-radius: 8px;
  flex: 1 1 0;
  max-width: 280px;
  box-shadow: 0 2px 6px rgba(0, 123, 255, 0.15);
  display: flex;
  flex-direction: column;
  min-height: 260px
}

.step-title {
  background-color: #1364FF;
  color: #ffffff;
  padding: 8px 12px;
  font-weight: bold;
  font-size: 14px;
  line-height: 1.2;
  text-align: center;
  border-top-left-radius: 8px;
  border-top-right-radius: 8px;
}

.subtitle {
  font-size: 11px;
  opacity: 0.9;
}

.step-content {
  padding: 16px;
  color: #333333;
  font-size: 14px;
  line-height: 1.5;
}

.step-content b {
  font-weight: 600;
}

.step-content p {
  margin: 0 0 10px;
  padding-left: 1.4em;  /* 왼쪽 여백 확보 */
  position: relative;
}

.step-content p::before {
  content: "•";
  position: absolute;
  left: 0;  /* 왼쪽 여백 내부에서 시작 */
  top: 0;
  font-weight: bold;
  color: #333;
}

.arrow-horizontal {
  align-self: center;
  font-size: 32px;
  color: #007bff;
  font-weight: bold;
}

.arrow-row {
  display: flex;
  justify-content: space-between;
  margin-top: -16px;
  margin-bottom: -16px;
}

.arrow-row div {
  flex: 1 1 0;
  display: flex;
  justify-content: center;
}


  </style>
</head>
<body>
  <div class="flow-container">


<!-- 위쪽 3단계 -->
<div class="row">
  <div class="step-box">
    <div class="step-title">1. Asset Transfer Request<br><span class="subtitle">(Originator → Ordering VASP)</span></div>
    <div class="step-content">
      <p>The Originator requests a virtual asset transfer from their Ordering VASP.</p>
      <p>The Ordering VASP collects the required Originator and Beneficiary information as specified by the TravelRule protocol.</p>
    </div>
  </div>
  <div class="step-box">
    <div class="step-title">2. Send Verification Request<br><span class="subtitle">(Ordering VASP → Beneficiary VASP)</span></div>
    <div class="step-content">
      <p>Using the collected information, the Ordering VASP sends a verification request via the Enclave server to the VerifyVASP Central Server, which then relays it to the Beneficiary VASP.</p>
      <p>All request data is protected with end-to-end encryption.</p>
    </div>
  </div>
  <div class="step-box">
    <div class="step-title">3. Perform Verification<br><span class="subtitle">(Beneficiary VASP)</span></div>
    <div class="step-content">
      <p>The Beneficiary VASP verifies the beneficiary information against its own customer records</p>
    </div>
  </div>
</div>

<!-- 아래쪽 3단계 -->
<div class="row">
  <div class="step-box">
    <div class="step-title">4. Return Verification Result<br><span class="subtitle">(Beneficiary VASP → Ordering VASP)</span></div>
    <div class="step-content">
      <p>The verification result is sent from the Beneficary VASP's Enclave, through the VerifyVASP Central Server, to the Ordering VASP.</p>
      <p>This process is handled asynchronously. See the relevant flow and API specifications for details.</p>
    </div>
  </div>
  <div class="step-box">
    <div class="step-title">5. Excute Transaction<br><span class="subtitle">(Ordering VASP)</span></div>
    <div class="step-content">
      <p>If the verification result is valid, the Ordering VASP executes the withdrawal on the blockchain.</p>
    </div>
  </div>
  <div class="step-box">
    <div class="step-title">6. Report Transaction Result<br><span class="subtitle">(Ordering VASP → Beneficiary VASP)</span></div>
    <div class="step-content">
      <p>The Ordering VASP sends a Report message containing the executed transaction’s TxHash to the Beneficiary VASP, confirming the transfer’s completion.</p>
    </div>
  </div>
</div>


  </div>
</body>
</html>
`}</HTMLBlock>

<br />

### VerifyName Verification Process

The VerifyName protocol can be used between a regulated VASP and an unregulated VASP.\
Depending on their regulatory status, the process may involve pre-verification or post-verification.
See the Scenarios and Flows and Integration Guide for detailed API specs and integration steps.

<HTMLBlock>{`
<div class="flow-container">
 <!-- 위쪽 3단계 -->
 <div class="row">
  <div class="step-box">
   <div class="step-title">
    1. Asset Transfer Request
    <br/>
    <span class="subtitle">
     (Originator → Ordering VASP)
    </span>
   </div>
   <div class="step-content">
    <p>
     The Originator requests a virtual asset transfer from the Ordering VASP.
    </p>
    <p>
     The Ordering VASP collects the beneficiary account information from the user.
    </p>
   </div>
  </div>
  <div class="step-box">
   <div class="step-title">
    2. Pre-Verification Request
    <br/>
    <span class="subtitle">
     (Ordering VASP → Beneficiary VASP)
    </span>
   </div>
   <div class="step-content">
    <p>
     <span style="color:#ff4d4f; font-weight:600;">
      Performed only when the Ordering VASP is a regulated VASP.
     </span>
    </p>
    <p>
     The Ordering VASP sends the hashed name and DOB of the Originator, along with the deposit address, to Beneficiary VASP for verification.
    </p>
    <p>
     The Beneficiary VASP verifies the validity of the deposit address and the ownership information.
    </p>
   </div>
  </div>
  <div class="step-box">
   <div class="step-title">
    3. Report Result & Execute Transaction
    <br/>
    <span class="subtitle">
     (Ordering VASP)
    </span>
   </div>
   <div class="step-content">
    <p>
     The Ordering VASP finalizes the withdrawal decision based on the verification result and sends a Report to the counterparty VASP. 
    </p>
    <p>
     If verification is successful, the Ordering VASP executes the withdrawal on the blockchain.
    </p>
    <p>
     If the Ordering VASP is unregulated, it may execute the withdrawal immediately without pre-verification.
    </p>
   </div>
  </div>
 </div>
 <!-- 아래쪽 3단계 -->
 <div class="row">
  <div class="step-box">
   <div class="step-title">
    4. Detect Deposit
    <br/>
    <span class="subtitle">
     (Beneficiary VASP)
    </span>
   </div>
   <div class="step-content">
    <p>
     The Beneficiary VASP monitors the blockchain for an incoming transaction to the specified deposit address.
    </p>
   </div>
  </div>
  <div class="step-box">
   <div class="step-title">
    5. Post-Verification Request
    <br/>
    <span class="subtitle">
     (Beneficiary VASP → Ordering VASP)
    </span>
   </div>
   <div class="step-content">
    <p>
     <span style="color:#ff4d4f; font-weight:600;">
      Performed only when the Beneficiary VASP is a regulated VASP.
     </span>
    </p>
    <p>
     The Beneficiary VASP requests TXID validation and the Originator information verification from the Ordering VASP.
    </p>
    <p>
     The Ordering VASP compares the hashed name and DOB of the Originator with the provided TXID to confirm identity.
    </p>
   </div>
  </div>
  <div class="step-box">
   <div class="step-title">
    6. Report Result
    <br/>
    <span class="subtitle">
      (Regluated VASP)
    </span>
   </div>
   <div class="step-content">
    <p>
     The regulated VASP confirms the final deposit decision based on the verification result and sends a Report to the counterparty VASP.
    </p>
    <p>
     If necessary, the Beneficiary VASP may inform the user of a deposit failure or hold reason.
    </p>
   </div>
  </div>
 </div>
</div>
`}</HTMLBlock>

<br />

***

<br />

## Security Considerations

### End-to-End Encryption Between VASPs

VerifyVASP applies end-to-end encryption (E2EE) to protect the integrity and privacy of personal data exchanged during the verification process.\
Only the Ordering VASP and the Beneficiary VASP can decrypt the data. The VerifyVASP Central Server never decrypts or stores it.

Each Enclave generates its own asymmetric key pair and securely stores it in its dedicated database. The private key never leaves the Enclave and is used only for encryption and decryption inside the Enclave.

Key management (generation, storage, rotation) is fully automated within the Enclave. VASPs do not need to implement their own key handling logic.

<br />

<HTMLBlock>{`
<style>
  .scenario-section {
    border: 1px dashed #ccc;
    border-radius: 8px;
    padding: 20px;
    margin-bottom: 32px;
    background-color: #fdfdfd;
  }

  .scenario-title {
    font-weight: bold;
    font-size: 16px;
    margin-bottom: 12px;
  }

  .step-list {
    list-style: none;
    padding-left: 0;
  }

  .step-item {
    margin-bottom: 12px;
  }

  .step-sublist {
    margin-top: 6px;
    margin-left: 10px;
    padding-left: 0;
  }

  .step-item > strong {
    display: block;
    margin-bottom: 10px;
  }

  .step-subitem {
    list-style-type: disc;
    margin-left: 5px;
    margin-bottom: 8px;
    font-size: 14px;
  }

</style>

<div class="scenario-section">
  <div class="scenario-title">Key Exchange</div>
  <ul class="step-list">
    <li class="step-item">
      <strong>Check for existing public key (Ordering VASP → Beneficiary VASP)</strong>
      <ul class="step-sublist">
        <li class="step-subitem">If the Ordering VASP does not have the Beneficiary VASP's public key, it requests one via the Central Server.</li>
      </ul>
    </li>
    <li class="step-item">
      <strong>Generate key pair (Beneficiary VASP → Ordering VASP)</strong>
      <ul class="step-sublist">
        <li class="step-subitem">The Beneficiary VASP either retrieves an existing public key or generates a new key pair and sends the public key back via the Central Server.</li>
      </ul>
    </li>
    <li class="step-item">
      <strong>Derive shared key (Ordering VASP → Beneficiary VASP)</strong>
      <ul class="step-sublist">
        <li class="step-subitem">The Ordering VASP uses the Beneficiary's public key and its own private key to derive a shared key, and includes its own public key with the encrypted request.</li>
      </ul>
    </li>
  </ul>
</div>
<div class="scenario-section">
  <div class="scenario-title">Encryption & Decryption</div>
  <ul class="step-list">
    <li class="step-item">
      <strong>Encrypt request data (Ordering VASP → Beneficiary VASP)</strong>
      <ul class="step-sublist">
        <li class="step-subitem">Personal data is encrypted with the shared key before sending the verification request.</li>
      </ul>
    </li>
    <li class="step-item">
      <strong>Decrypt request and encrypt response (Beneficiary VASP)</strong>
      <ul class="step-sublist">
        <li class="step-subitem">The Beneficary VASP decrypts the request, verifies the data, and encrypts the verification result.</li>
      </ul>
    </li>
    <li class="step-item">
      <strong>Decrypt verification result (Ordering VASP)</strong>
      <ul class="step-sublist">
        <li class="step-subitem">The Ordering VASP decrypts the result using the shared key and passes it to its backend.</li>
      </ul>
    </li>
  </ul>
</div>
`}</HTMLBlock>

<br />

### Security Options

#### Public Key Caching

> * Configure caching duration via VEGA\_PUBLIC\_KEY\_TTL (milliseconds).
> * Default: 1800000 (30 min)
> * Minimum: 600000 (10 min)

<br />

#### Public Key Types (keyType)

| keyType           | Description                           | Pros                    | Cons                     |
| :---------------- | :------------------------------------ | :---------------------- | :----------------------- |
| `PerVasp`         | One key pair per VASP                 | High caching efficiency | Lower security           |
| `PerAddress`      | One key pair per beneficiary address  | Higher security         | Lower caching efficiency |
| `PerVerification` | New key pair per verification request | Highest security        | No caching               |

> ⚠️ Note:
>
> * `keyType` option is upported in TravelRule and VerifyName 2.0..
> * For Verifyname, the setting applies only when both parties use VerifyName 2.0.
> * If the counterparty uses VerifyName 1.0, keys are always generated as `PerVASP`.

***

<br />

## VASP Implementation Requirements

To integrate with VerifyVASP’s TravelRule and VerifyName 2.0 solutions, VASPs must complete the following steps in sequence. For detailed instructions, refer to the corresponding sections in the related documentation.

<HTMLBlock>{`
<style>
  .vasp-step-list {
    list-style: none;
    padding: 0;
    margin: 0;
  }

  .vasp-step-item {
    display: flex;
    align-items: flex-start;
    margin-bottom: 20px;
  }

  .vasp-step-badge {
    background-color: #000;
    color: #fff;
    font-weight: bold;
    border-radius: 50%;
    width: 24px;
    height: 24px;
    text-align: center;
    line-height: 24px;
    font-size: 13px;
    margin-right: 10px;
    flex-shrink: 0;
  }

  .vasp-step-content {
    flex: 1;
    font-size: 14px;
    line-height: 1.6;
  }

  .vasp-step-title {
    font-weight: bold;
    font-size: 15px;
    margin-bottom: 4px;
  }
</style>

<ol class="vasp-step-list">
  <li class="vasp-step-item">
    <div class="vasp-step-badge">1</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">Review key scenarios and flows</div>
      Beo구현에 앞서 <a href="ref:travelrule-flow-diagram">Best Practice</a> 를 통해 입출금 시나리오에서의 요청 순서와 통신 흐름을 확인합니다.
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">2</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">구현 범위 확인 및 계획 수립</div>
      To-Be 아키텍처를 기반으로 실제 구현 범위를 확인하고 연동 계획을 수립합니다.
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">3</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">VerifyVASP 온보딩</div>
      VerifyVASP Alliance에 가입하고 인증 절차를 완료하여 연동에 필요한 자격 증명(Credential)을 발급받습니다.
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">4</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">VASP API 개발</div>
      검증을 처리하기 위한 VASP 자체 API를 구현하고, Enclave 서버가 해당 API를 호출할 수 있도록 연동합니다.
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">5</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">Enclave 데이터베이스 구성</div>
      Enclave 설치에 앞서, 검증 및 규제 대응 데이터를 안전하게 저장할 수 있는 전용 데이터베이스를 구축합니다.
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">6</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">Enclave 설치</div>
      VASP 인프라 내에 Enclave 서버를 Docker 환경으로 설치하고, VASP 백엔드와 연결하여 VerifyVASP 중앙 서버와의 통신을 준비합니다.
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">7</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">Robot VASP를 통한 시나리오 테스트</div>
      Robot VASP를 활용한 시나리오 기반 테스트를 통해 구현 결과를 검증하고 프로토콜 준수 여부를 확인합니다.
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">8</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">지속적 유지관리 계획 수립</div>
      규제 변경 및 프로토콜 업데이트에 대응할 수 있도록 유지관리 계획을 마련합니다.
    </div>
  </li>
</ol>
`}</HTMLBlock>