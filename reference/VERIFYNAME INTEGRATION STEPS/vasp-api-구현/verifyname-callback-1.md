---
title: Callback API
excerpt: >
  The Callback API serves as a common interface for handling various
  asynchronous flows. Depending on the callbackType, it may be invoked when your
  VASP acts as either the Originating VASP or the Beneficiary VASP. Your
  implementation must function correctly in all possible cases. The Enclave will
  invoke this API when a counterparty VASP triggers a Report APIs.


  ## Functional Requirements
    **1. Callback Types Support**
    The implementation must route the request to appropriate business logic for each `callbackType` values. The following are the possible values for the `callbackTypes` field and the scenarios in which they are used:
    - **`OWNER_VERIFICATION_RESULT_REPORT`**: This callback is triggered by the Enclave when the final Owner Verification result is reported.
    - **`OWNER_VERIFICATION_TX_REPORT`**: This callback is triggered by the Enclave when the Originating VASP reports the execution of a transfer transaction.

    `OWNER_VERIFICATION_RESULT_REPORT` and `OWNER_VERIFICATION_TX_REPORT` type must be implemented as mandatory processing logic.

    **2.Callback Handling**
    When a Owner Verification result report is received, your VASP can perform the following actions.
    - If the result is `VERIFIED`, your VASP can proceed with the asset transfer.
    - If the result is `DENIED` or `ERROR`, your VASP can stop the asset transfer or deposit process and notify it to the Originator.

    When a Transaction result report is received, your VASP can perform the following actions.
    - Match the reported on-chain transaction hash with the actual deposit to the beneficiary address.
    - Confirm that the asset transfer has been requested and log the details.

  <details>
    <summary>Example of Request Body for OWNER_VERIFICATION_RESULT_REPORT callback type.</summary>

    ``` json
      // VERIFIED
      {
        "callbackType": "OWNER_VERIFICATION_RESULT_REPORT",
        "data": {
          "request_id": "64ab871b-14a3-47df-9b80-368e29fe8181",
          "reported_result": "VERIFIED",
        }
      }

      or

      // DENIED
      {
        "callbackType": "OWNER_VERIFICATION_RESULT_REPORT",
        "data": {
          "request_id": "64ab871b-14a3-47df-9b80-368e29fe8181",
          "reported_result": "DENIED",
          "reason": "MISMATCH-TICKER"
          "message": "Ticker is mismatched."
        }
      }

      or

      // ERROR
      {
        "callbackType": "OWNER_VERIFICATION_RESULT_REPORT",
        "data": {
          "request_id": "64ab871b-14a3-47df-9b80-368e29fe8181",
          "reported_result": "ERROR",
          "reason": "TRANSFER-ERROR",
          "message": "Transfer is failed."
        }
      }
    ```
  </details>

  <details>
    <summary>Example of Request Body for OWNER_VERIFICATION_TX_REPORT callback type.</summary>

    ``` json
      {
        "callbackType": "OWNER_VERIFICATION_TX_REPORT",
        "data": {
          "request_id": "64ab871b-14a3-47df-9b80-368e29fe8181",
          "tx_hash": "0xd231a7c7ff1edba061e3fbde26fe0e567fde0d2c40ff40ad1a9f3bffd999f128"
        }
      }
    ```
  </details>


  ## Constraints
    - This API must respond within 1 second.
    - Only the HTTP status code 200 OK can be returned. Other response status codes are not allowed.
    - To maintain data consistency and reliability, the Callback API must guarantee idempotency. 
      - This ensures that if the same Callback API request is received multiple times from the Enclave, the data state and response remain unchanged after the initial call. 
      - For example, you can implements your VASP to ignore the duplicate requests.

  ## Recommendations
    Since the API response must be returned as quickly as possible, time-consuming tasks within the Callback API should be handled asynchronously.

  ## Environment Variable Configuration
    Set the following environment variables as per the guide to integrate the implemented API with the Enclave.
    - `VEGA_VERIFICATION_CALLBACK_API_PATH`: Implement this API at the desired path({`VASP_DEFINED_PATH_CALLBACK`}) and set the path in the variable.
    - `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: Set this variable to the API key provided during your VerifyVASP onboarding process.

    For a complete list of Enclave environment variables, [click here.](ref:verifyname-enclave-setup/environment-variables)
api:
  file: VN_VASP_API_KR_Spec.yaml
  operationId: verifyName-Callback
hidden: false
---
이 API는 비동기적 콜백 상황을 처리하기 위한 공통 인터페이스입니다. Enclave는 상대 VASP로부터 Report API가 호출되었을 때 이 API를 실행합니다.

***

## 구현 가이드

### 기능 요구사항

#### 1. 콜백 타입 분기 처리

요청의 `callbackType` 필드에 따라 각 콜백 유형에 맞는 비즈니스 로직으로 분기 처리해야 합니다. 지원해야 하는 콜백 유형은 아래와 같으며, `OWNER_VERIFICATION_RESULT_REPORT`, `OWNER_VERIFICATION_TX_REPORT` 모두 필수 구현 대상입니다.

<HTMLBlock>{`
<style>
  .custom-table {
    border-collapse: collapse;
    width: 100%;
    font-size: 14px;
  }

  .custom-table th,
  .custom-table td {
    border: 1px solid #ddd;
    padding: 12px;
    text-align: left;
    vertical-align: top;
  }

  .custom-table th {
    background-color: #f0f0f0;
    font-weight: 600;
  }

  .custom-table td {
    background-color: #ffffff;
  }

  .custom-table td.code-col {
    min-width: 200px;
    white-space: nowrap;
  }
</style>

<table class="custom-table">
  <thead>
    <tr>
      <th><code>callbackType</code></th>
      <th>설명</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="code-col"><code>OWNER_VERIFICATION_RESULT_REPORT</code></td>
      <td>상대 VASP로 요청한 계정 소유자 검증이 완료되어 결과가 비동기적으로 전달될 때 호출됩니다.</td>
    </tr>
    <tr>
      <td class="code-col"><code>OWNER_VERIFICATION_TX_REPORT</code></td>
      <td>수신 VASP 역할에서 사용됩니다. 송신 VASP가 트랜잭션 결과를 Report할 때 호출됩니다.</td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

<Callback />

<br />

#### 2. OWNER\_VERIFICATION\_RESULT\_REPORT 유형 콜백 처리

콜백으로 수신한 검증 결과에 따라 후속 조치를 수행해야 합니다.

* 사전 검증 결과가 VERIFIED인 경우, 이어서 자산 전송 트랜잭션을 실행합니다.
* 사전 검증 결과가 DENIED 또는 ERROR인 경우, 자산 전송을 중단하고 송신자(사용자)에게 결과를 통지합니다.

`OWNER_VERIFICATION_RESULT_REPORT` 유형 콜백 메시지 예시는 아래와 같습니다.

<HTMLBlock>{`
<style>
  .custom-accordion {
    border: 1px solid #d0d7de;
    border-radius: 8px;
    margin-bottom: 12px;
    overflow: hidden;
    transition: border 0.3s ease;
  }

  .custom-accordion[open] {
    border: 2px solid #1d78ff;
  }

  .custom-accordion summary {
    padding: 12px 16px;
    cursor: pointer;
    list-style: none;
    font-weight: 500;
    background-color: #f9f9f9;
  }

  .custom-accordion summary::marker,
  .custom-accordion summary::-webkit-details-marker {
    display: none;
  }

  .custom-accordion summary::before {
    content: "›";
    display: inline-block;
    margin-right: 8px;
    transform: rotate(0deg);
    transition: transform 0.2s ease;
  }

  .custom-accordion[open] summary::before {
    transform: rotate(90deg);
  }

  .custom-accordion pre {
    background-color: #ffffff;
    padding: 16px;
    margin: 0;
    font-size: 14px;
    overflow-x: auto;
  }
</style>
<details class="custom-accordion">
  <summary>Example of Callback Request Body: OWNER_VERIFICATION_RESULT_REPORT</summary>

  <pre><code class="language-json">
  // VERIFIED
  {
    "callbackType": "OWNER_VERIFICATION_RESULT_REPORT",
    "data": {
      "request_id": "64ab871b-14a3-47df-9b80-368e29fe8181",
      "reported_result": "VERIFIED",
    }
  }

  or

  // DENIED
  {
    "callbackType": "OWNER_VERIFICATION_RESULT_REPORT",
    "data": {
      "request_id": "64ab871b-14a3-47df-9b80-368e29fe8181",
      "reported_result": "DENIED",
      "reason": "MISMATCH-TICKER"
      "message": "Ticker is mismatched."
    }
  }

  or

  // ERROR
  {
    "callbackType": "OWNER_VERIFICATION_RESULT_REPORT",
    "data": {
      "request_id": "64ab871b-14a3-47df-9b80-368e29fe8181",
      "reported_result": "ERROR",
      "reason": "TRANSFER-ERROR",
      "message": "Transfer is failed."
    }
  }
  </code></pre>
</details>
`}</HTMLBlock>

<br />

#### 3. OWNER\_VERIFICATION\_TX\_REPORT 유형 콜백 처리

트랜잭션 결과 Report를 수신한 경우, 다음과 같은 후속 작업을 진행할 수 있습니다.

* 보고된 온체인 트랜잭션 Hash가 실제 수신자 주소로의 입금 건과 일치하는지 확인합니다.
* 자산 전송 요청이 이루어졌는지 여부를 확인하고 관련 정보를 기록합니다.

`OWNER_VERIFICATION_TX_REPORT` 유형 콜백 메시지 예시는 아래와 같습니다.

<HTMLBlock>{`
<details class="custom-accordion">
  <summary>Example of Callback Request Body: OWNER\_VERIFICATION\_TX\_REPORT</summary>

  <pre><code class="language-json">
 {
    "callbackType": "OWNER_VERIFICATION_TX_REPORT",
    "data": {
      "request_id": "64ab871b-14a3-47df-9b80-368e29fe8181",
      "tx_hash": "0xd231a7c7ff1edba061e3fbde26fe0e567fde0d2c40ff40ad1a9f3bffd999f128"
    }
  }
  </code></pre>
</details>
`}</HTMLBlock>

<br />

### 제약 조건

* 이 API는 1초 이내에 응답해야 합니다.
* 응답의 HTTP 상태 코드는 반드시 200 OK로 반환해야 합니다.
* 동일한 콜백 요청이 여러 번 수신되어도 처리 결과가 동일하도록 멱등성을 보장해야 합니다.\
  (ex) 중복 요청 시 내부 처리 로직에서 무시하도록 구현

### 구현 권장사항

* 콜백 API의 경우 응답 속도가 중요하므로, 시간 소모가 큰 작업은 응답 이후 비동기 방식으로 처리하는 것을 권장합니다.

### Enclave 연동 설정

Enclave와의 정상 연동을 위해 아래 환경 변수를 설정해야 합니다.

* `VEGA_VERIFICATION_CALLBACK_API_PATH`: 해당 API 경로
* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: VerifyVASP 온보딩 시 발급받은 API Key

***

## API 명세