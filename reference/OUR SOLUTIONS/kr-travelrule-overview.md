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

### VerifyName 검증 프로세스

VerifyName 프로토콜의 경우 TravelRule 프로토콜과 달리 Travel Rule 규제 의무가 있는 VASP와 규제 의무가 없는 VASP간 수행될 수 있습니다. 송신 VASP와 수신 VASP의 규제 준수 여부에 따라 사전 검증 또는 사후 검증 형태로 진행됩니다. 각 단계별로 호출되는 API 명세 및 기술 연동 과정은 Scenarios and Flows 문서 및 Integration Guide에서 확인할 수 있습니다.

<HTMLBlock>{`
<div class="flow-container">
 <!-- 위쪽 3단계 -->
 <div class="row">
  <div class="step-box">
   <div class="step-title">
    1. 자산 전송 요청
    <br/>
    <span class="subtitle">
     (Originator → Ordering VASP)
    </span>
   </div>
   <div class="step-content">
    <p>
     <b>송신 VASP의 사용자(송신자, Originator)</b>가 자산 전송을 요청합니다.
    </p>
    <p>
     <b>
      송신 VASP
     </b>
     는 수신 계좌 정보를 사용자로부터 수집합니다.
    </p>
   </div>
  </div>
  <div class="step-box">
   <div class="step-title">
    2. 사전 검증 요청
    <br/>
    <span class="subtitle">
     (Ordering VASP → Beneficiary VASP)
    </span>
   </div>
   <div class="step-content">
    <p>
     <span style="color:#ff4d4f; font-weight:600;">
      송신 VASP가 Regulated VASP인 경우 수행
     </span>
    </p>
    <p>
     <b>송신 VASP</b>는 송신자의 이름과 생년월일의 Hash값과 입금 주소를 수신 VASP로 전달하여 검증을 요청합니다.
    </p>
    <p>
     <b>수신 VASP</b>는 주소의 유효성과 입금 주소 소유주 정보를 검증합니다.
    </p>
   </div>
  </div>
  <div class="step-box">
   <div class="step-title">
    3. 결과 Report 및 트랜잭션 실행
    <br/>
    <span class="subtitle">
     (Ordering VASP)
    </span>
   </div>
   <div class="step-content">
    <p>
     송신 VASP는 검증 결과를 기반으로 최종 출금 여부를 확정하고 상대 VASP로 Report해야 합니다. 
    </p>
    <p>
     검증 결과가 정상인 경우 송신 VASP는 블록체인 상에서 출금 트랜잭션을 실행합니다.
    </p>
    <p>
     송신 VASP가 Unregulated VASP인 경우 별도의 사전 검증 없이 즉시 출금 트랜잭션을 실행할 수 있습니다.  
    </p>
   </div>
  </div>
 </div>
 <!-- 아래쪽 3단계 -->
 <div class="row">
  <div class="step-box">
   <div class="step-title">
    4. 입금 트랜잭션 감지
    <br/>
    <span class="subtitle">
     (Beneficiary VASP)
    </span>
   </div>
   <div class="step-content">
    <p>
     <b>수신 VASP</b>는 블록체인 상에서 입금 주소로의 입금 트랜잭션을 감지합니다.
    </p>
   </div>
  </div>
  <div class="step-box">
   <div class="step-title">
    5. 사후 검증 요청
    <br/>
    <span class="subtitle">
     (Beneficiary VASP → Ordering VASP)
    </span>
   </div>
   <div class="step-content">
    <p>
     <span style="color:#ff4d4f; font-weight:600;">
      수신 VASP가 Regulated VASP인 경우 수행
     </span>
    </p>
    <p>
     <b>수신 VASP</b>는 송신 VASP에게 TXID 유효성 및 송신자 정보 검증을 요청합니다.
    </p>
    <p>
     <b>송신 VASP</b>는 TXID에 해당하는 송신자 이름 및 생년월일의 Hash값을 비교하여 동일인 여부를 판단합니다.
    </p>
   </div>
  </div>
  <div class="step-box">
   <div class="step-title">
    6. 결과 Report
    <br/>
    <span class="subtitle">
      (Regluated VASP)
    </span>
   </div>
   <div class="step-content">
    <p>
     Regulated VASP는 검증 결과를 기반으로 입금 최종 반영 여부를 확정하고 상대 VASP로 Report해야 합니다. 
    </p>
    <p>
     필요 시 사용자에게 입금 실패 또는 보류 사유를 안내할 수 있습니다.
    </p>
   </div>
  </div>
 </div>
</div>
`}</HTMLBlock>

<br />

***

<br />

## 보안 고려사항

### VASP 간 End-to-End 암호화

VerifyVASP 솔루션은 송,수신인 검증 과정에서 교환되는 개인정보의 무결성과 프라이버시를 보호하기 위해 송신 VASP와 수신 VASP 사이 통신 구간에 End-to-End 암호화(E2EE)를 적용합니다. 오직 송신 VASP와 수신 VASP만이 데이터를 복호화 할 수 있으며, VerifyVASP 중앙 서버는 데이터를 복호화하거나 저장하지 않습니다.

VerifyVASP 프로토콜은 비대칭키 기반 암호화를 채택하고 있으며, 각 VASP에 설치된 Enclave 서버는 자체적으로 비대칭 키 쌍을 생성하고 이를 Enclave 전용 데이터베이스에 안전하게 저장합니다. 생성된 키 쌍은 공개키와 개인키로 구성되며, 개인키는 외부 노출 없이 Enclave 내부에서만 암복호화 작업에 사용됩니다.

모든 키 관리 절차(생성, 저장, 갱신)는 Enclave 내에서 자동화된 방식으로 처리되므로, 각 VASP는 키 관리 기능을 별도로 구현할 필요 없이 표준화된 방식으로 End-to-End 암호화를 적용하여 안전하게 검증을 수행할 수 있습니다. 프로토콜의 수행 단계는 다음과 같습니다.

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
  <div class="scenario-title">키 교환 (Key Exchange)</div>
  <ul class="step-list">
    <li class="step-item">
      <strong>공개 키 요청 (Ordering VASP → Beneficiary VASP)</strong>
      <ul class="step-sublist">
        <li class="step-subitem">데이터 암호화를 수행하기에 앞서, 송신 VASP의 Enclave는 저장된 수신 VASP의 공개키가 있는지 확인합니다.</li>
        <li class="step-subitem">사용 가능한 공개키가 없는 경우 송신 VASP Enclave는 중앙 서버를 통해 수신 VASP Enclave로 공개키를 요청합니다.</li>
      </ul>
    </li>
    <li class="step-item">
      <strong>키 쌍 생성 및 공개키 반환 (Beneficiary VASP → Ordering VASP)</strong>
      <ul class="step-sublist">
        <li class="step-subitem">수신 VASP Enclave는 키 생성 정책에 따라 적절한 공개키를 조회하거나 새로운 키 쌍을 생성한 뒤 저장합니다.</li>
        <li class="step-subitem">공개키가 VerifyVASP 중앙 서버를 통해 송신 VASP Enclave로 전달됩니다.</li>
      </ul>
    </li>
    <li class="step-item">
      <strong>공유 키 유도 및 공개 키 전달 (Ordering VASP → Beneficiary VASP)</strong>
      <ul class="step-sublist">
        <li class="step-subitem">송신 VASP Enclave는 키 생성 정책에 따라 적절한 공개키를 조회하거나 새로운 키 쌍을 생성한 뒤 저장합니다.</li>
        <li class="step-subitem">데이터 암복호화에 사용되는 공유 키는 상대방 VASP 의 공개 키와 자신의 VASP의 비밀 키로부터 키 교환 알고리즘을 이용해 유도됩니다.</li>
        <li class="step-subitem">송신 VASP의 공개키는 검증 요청이 수신 VASP로 전달될 때 암호화된 개인 정보와 함께 전달됩니다.</li>
      </ul>
    </li>
  </ul>
</div>
<div class="scenario-section">
  <div class="scenario-title">데이터 암호화 및 복호화(Encryption & Decryption)</div>
  <ul class="step-list">
    <li class="step-item">
      <strong>개인 정보 암호화 및 요청 전송 (Ordering VASP → Beneficiary VASP)</strong>
      <ul class="step-sublist">
        <li class="step-subitem">송신 VASP Enclave는 검증 요청을 보내기 전 키 교환 과정에서 획득한 공유 키로 개인 정보 필드를 암호화합니다.</li>
        <li class="step-subitem">암호화된 개인 정보 필드를 포함한 검증 요청이 송신 VASP의 공개키와 함께 수신 VASP로 전달됩니다.</li>
      </ul>
    </li>
    <li class="step-item">
      <strong>검증 요청 내 개인 정보 복호화 및 결과 내 개인 정보 암호화(Beneficiary VASP)</strong>
      <ul class="step-sublist">
        <li class="step-subitem">검증 요청을 받은 수신 VASP Enclave는 암호화에 사용된 공개키와 한 쌍인 비밀키를 조회하여 복호화를 수행합니다.</li>
        <li class="step-subitem">개인정보 원문(Plaintext) 중 필요한 정보를 Enclave 데이터베이스에 저장하고, 백엔드로 전달하여 검증을 수행합니다.</li>
        <li class="step-subitem">검증 결과에 포함되는 개인 정보를 키 교환 과정에서 획득한 공유 키로 암호화합니다.</li>
      </ul>
    </li>
    <li class="step-item">
      <strong>검증 결과 내 개인 정보 복호화 (Ordering VASP)</strong>
      <ul class="step-sublist">
        <li class="step-subitem">송신 VASP Encalve는 검증 결과를 수신한 뒤, 암호화된 개인 정보 필드를 키 교환 과정에서 획득한 공유 키로 복호화합니다.</li>
        <li class="step-subitem">개인정보 원문에 대해 필요한 정보를 Enclave 데이터베이스에 저장하고, 검증 결과를 송신 VASP 백엔드로 전달합니다.</li>
      </ul>
    </li>
  </ul>
</div>
`}</HTMLBlock>

<br />

### 보안 옵션

Enclave 서버는 암호화 처리를 전자동으로 수행하지만, 구성 유연성을 위해 일부 설정 옵션을 제공합니다. 적절한 설정을 통해 우수한 보안 수준을 유지하면서 보다 효율적으로 VASP간에 데이터를 교환 할 수 있습니다.

<br />

#### 공개키 Caching 설정

효율적인 공개키 관리를 위해 Enclave 서버는 상대 VASP의 공개키를 설정한 시간 동안 Caching할 수 있습니다. 적절한 Caching을 통해 키 교환을 위한 반복 요청을 줄임으로써 검증 시간을 최소화 할 수 있습니다.

> Enclave 환경 변수 - `VEGA_PUBLIC_KEY_TTL`에 Caching 유효시간(TTL, Time-to-Live)을 밀리초 단위로 설정합니다.
>
> 캐싱 유효시간 기본값은 1800000(30분)이며 최소값은 600000(10분)입니다.

<br />

#### 공개키 타입(keyType) 설정

Enclave 서버는 다양한 공개키 타입을 지원합니다. 검증 요청 시 원하는 keyType을 지정하여 상대 VASP와 동일한 암호화 키 갱신 주기를 사용할 수 있습니다. 지원하는 keyType은 다음과 같습니다.

<Table align={["left","left","left","left"]}>
  <thead>
    <tr>
      <th>
        keyType
      </th>

      <th>
        Description
      </th>

      <th>
        Pros
      </th>

      <th>
        Cons
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        `PerVasp`
      </td>

      <td>
        동일 VASP에 대해 하나의 키 쌍을 사용
      </td>

      <td>
        높은 Caching 효율성
      </td>

      <td>
        모든 요청에 같은 키를 사용,
        상대적으로 낮은 보안성
      </td>
    </tr>

    <tr>
      <td>
        `PerAddress`
      </td>

      <td>
        수신자 주소별로 서로 다른 키 쌍을 사용
      </td>

      <td>
        PerVasp 대비
        높은 보안성
      </td>

      <td>
        Caching 효율성 낮음
      </td>
    </tr>

    <tr>
      <td>
        `PerVerification`
      </td>

      <td>
        매 검증 요청마다 새로운 키 쌍을 사용
      </td>

      <td>
        가장 높은 보안성 제공
      </td>

      <td>
        Caching 사용 불가대
      </td>
    </tr>
  </tbody>
</Table>

> ⚠️ VerifyName 프로토콜의 `keyType` 옵션은 VerifyName 2.0부터 지원됩니다.
>
> Enclave API의 `keyType` 옵션은 TravelRule과 VerifyName 2.0 프로토콜에서 모두 지원됩니다. 단, VerifyName 프로토콜에서는 요청을 처리하는 상대 VASP도 VerifyName 2.0 프로토콜을 지원하는 경우에만 지정한 `keyType`에 따른 키 생성 주기가 반영됩니다. 상대 VASP가 VerifyName 1.0 프로토콜을 사용하는 경우 상대 VASP에서 암호화 키는 항상 `PerVasp` 타입으로 생성 및 관리됩니다.

***

<br />

## VASP 작업 요구사항

VerifyVASP가 제공하는 TravelRule과 VerifyName 2.0 솔루션 연동을 위해 VASP는 다음의 절차를 순차적으로 수행해야 합니다. 각 항목에 대한 세부 내용은 관련 문서를 참조하세요.

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
      <div class="vasp-step-title">주요 시나리오 및 흐름 검토</div>
      구현에 앞서 <a href="ref:travelrule-flow-diagram">Best Practice</a> 를 통해 입출금 시나리오에서의 요청 순서와 통신 흐름을 확인합니다.
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