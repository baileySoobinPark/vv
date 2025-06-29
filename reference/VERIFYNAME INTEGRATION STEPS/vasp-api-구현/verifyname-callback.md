---
title: Callback API
api:
  file: VN_VASP_API_Spec.yaml
  operationId: verifyName-Callback
hidden: false
---
본 API는 비동기적 콜백 상황을 처리하기 위한 공통 인터페이스입니다. Enclave는 상대 VASP로부터 Report API가 호출되었을 때 이 API를 실행합니다.

***

## 구현 가이드

### 기능 요구사항

#### 1. 콜백 타입 분기 처리

API 요청의 `callbackType` 필드에 따라 각 콜백 유형에 맞는 비즈니스 로직으로 분기 처리해야 합니다. 지원해야 하는 콜백 유형은 아래와 같으며, `OWNER_VERIFICATION_RESULT_REPORT`, `OWNER_VERIFICATION_TX_REPORT` 모두 필수 구현 대상입니다.

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

* 사전 검증 결과가 VERIFIED인 경우, 상대 VASP에서 자산 전송 트랜잭션을 수행할 것입니다.
* 사전 검증 결과가 DENIED 또는 ERROR인 경우, 상대 VASP에서 자산 전송을 중단한 것입니다.

`OWNER_VERIFICATION_RESULT_REPORT` 유형 콜백 메시지 예시는 아래와 같습니다.

<Accordion title="Example of Callback: OWNER_VERIFICATION_RESULT_REPORT" icon="fa-info-circle">
  ```json
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
      "reason": "MISMATCH-TICKER",
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
      "reason": "UNDEFINED-ERROR",
      "message": "An internal error occurred during asset transfer."
    }
  }
  ```
</Accordion>

#### 3. OWNER\_VERIFICATION\_TX\_REPORT 유형 콜백 처리

트랜잭션 결과 Report를 수신한 경우, 다음과 같은 후속 작업을 진행할 수 있습니다.

* 보고된 온체인 트랜잭션 해시가 실제 수신인 주소로의 입금 건과 일치하는지 확인합니다.
* 자산 이전 요청이 이루어졌는지 확인하고 관련 정보를 기록합니다.

`OWNER_VERIFICATION_TX_REPORT` 유형 콜백 메시지 예시는 아래와 같습니다.

<Accordion title="Example of Callback: OWNER_VERIFICATION_TX_REPORT" icon="fa-info-circle">
  ```json
  {
    "callbackType": "OWNER_VERIFICATION_TX_REPORT",
    "data": {
      "request_id": "64ab871b-14a3-47df-9b80-368e29fe8181",
      "tx_hash": "0xd231a7c7ff1edba061e3fbde26fe0e567fde0d2c40ff40ad1a9f3bffd999f128"
    }
  }
  ```
</Accordion>

<br />

### 제약 조건

* 이 API는 1초 이내에 응답해야 합니다.
* 응답의 HTTP 상태 코드는 반드시 '200 OK'로 반환해야 합니다.
* 동일한 콜백 요청이 반복 수신되더라도 처리 결과가 변하지 않도록 멱등성을 반드시 보장해야 합니다.\
  (ex) 중복 요청인 경우 내부 처리 로직에서 이미 처리된 요청으로 간주하고 무시하도록 구현합니다.

### 구현 권장사항

* 콜백 API의 경우 응답 속도가 중요하므로, 시간 소모가 큰 작업은 응답 이후 비동기 방식으로 처리하는 것을 권장합니다.

### Enclave 연동 설정

Enclave와의 정상 연동을 위해 아래 환경 변수를 설정해야 합니다.

* `VEGA_VERIFICATION_CALLBACK_API_PATH`: 해당 API 경로
* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: API 인증을 위한 인증 토큰 값
* `VEGA_VERIFICATION_AUTHORIZATION_KEY`: API 인증 토큰을 전달할 header key

***

## API 명세