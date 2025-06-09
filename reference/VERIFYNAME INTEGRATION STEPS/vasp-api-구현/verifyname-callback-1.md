---
title: Callback API
api:
  file: VN_VASP_API_KR_Spec.yaml
  operationId: verifyName-Callback
hidden: false
---
이 API는 비동기적 콜백 상황을 처리하기 위한 공통 인터페이스입니다. Enclave는 상대 VASP로부터 Report API가 호출되었을 때 이 API를 실행합니다.

***

<Callback />

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

`data.result`가 DENIED 또는 ERROR인 경우 전달될 수 있는 실패 사유 코드는 아래와 같습니다.

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
    min-width: 150px;
    white-space: nowrap;
  }
</style>

<table class="custom-table">
  <thead>
    <tr>
      <th><code>reason</code></th>
      <th><code>message</code></th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="code-col"><code>UNKNOWN-SYMBOL</code></td>
      <td>미지원 자산 심볼<br>(ex)"ETH"</td>
      <td>지원하지 않는 가상자산 종목 (예: 거래소에서 미지원인 종목)</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNKNOWN-NETWORK</code></td>
      <td>미지원 네트워크 이름<br>(ex)"Ethereum"</td>
      <td>지원하지 않는 네트워크 (예: USDT-Ethereum 요청되었으나 거래소에서 USDT-Tron만 지원하는 경우)</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNKNOWN-ADDRESS</code></td>
      <td>대상 주소<br>(ex)"0xasd..."</td>
      <td>확인할 수 없는 지갑 주소</td>
    </tr>
    <tr>
      <td class="code-col"><code>LACK-OF-INFORMATION</code></td>
      <td>콤마(,)로 구분된 누락 필드 목록<br>(ex)"ACCOUNT_NUMBER"</td>
      <td>송신자 정보 부족</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNAVAILABLE-INFORMATION</code></td>
      <td>콤마(,)로 구분된 제공 불가 필드 목록<br>(ex)"ACCOUNT_NUMBER"</td>
      <td>제공 불가한 수신자 정보</td>
    </tr>
    <tr>
      <td class="code-col"><code>BLACKLISTED</code></td>
      <td>대상 주소<br>(ex)"0xasd..."</td>
      <td>제재 목록에 포함된 주소</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNVERIFIED-KYC</code></td>
      <td>-</td>
      <td>KYC 미완료</td>
    </tr>
    <tr>
      <td class="code-col"><code>MISMATCHED-NAME</code></td>
      <td>-</td>
      <td>수신자 이름 불일치</td>
    </tr>
    <tr>
      <td class="code-col"><code>NOT-ALLOWED</code></td>
      <td>해당 사유<br>(ex) "This user is locked by internal policy."</td>
      <td>내부 정책으로 인해 거부됨</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNDEFINED-ERROR</code></td>
      <td>-</td>
      <td>정의되지 않은 기타 오류</td>
    </tr>
    <tr>
      <td class="code-col"><code>BENEFICIARY-ACCOUNT-NOT-MATCHED</code></td>
      <td>-</td>
      <td>수신 VASP가 송신 VASP가 전송한 주소를 변경하여 반환한 경우</td>
    </tr>
    <tr>
      <td class="code-col"><code>REQUEST-TIMEOUT</code></td>
      <td>-</td>
      <td>검증 요청 후 대기시간이 초과한 경우</td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

#### 3. TX\_REPORT 유형 콜백 처리 (수신 VASP 역할)

콜백으로 수신한 트랜잭션 Hash가 수신자의 실제 입금 주소로 발생한 트랜잭션인지 확인하고 결과 및 이력을 데이터베이스에 기록합니다.

<HTMLBlock>{`
<details class="custom-accordion">
  <summary>Example of Callback Request Body</summary>

  <pre><code class="language-json">
{
   "callbackType":"TX_REPORT",
   "data":{
      "verificationUuid":"64ab871b-14a3-47df-9b80-368e29fe8181",
      "txHash":"8a54d58ca4100112a5430818776d74898f2232770bae03046862575cb851a042",
      "vout":"2"
   }
}
  </code></pre>
</details>
`}</HTMLBlock>

#### 4. ERROR\_REPORT 유형 콜백 처리 (수신 VASP 역할)

오류 보고 내용을 확인한 뒤 해당 전송을 취소한 뒤, 트랜잭션 추적을 중단하고 로그 기록을 남깁니다.

<HTMLBlock>{`
<details class="custom-accordion">
  <summary>Example of Callback Request Body</summary>

  <pre><code class="language-json">
{
   "callbackType":"ERROR_REPORT",
   "data":{
      "verificationUuid":"64ab871b-14a3-47df-9b80-368e29fe8182",
      "result":"ERROR",
      "reason":"BLACKLISTED",
      "message":"Originator is included in the blacklist."
   }
}
  </code></pre>
</details>
`}</HTMLBlock>

#### 5. CHAINALYSIS\_KYT\_RESULT 유형 콜백 처리 (선택사항)

콜백으로 수신한 Chainalysis KYT 리스크 평가 결과에 따라 다음과 같은 작업을 수행할 수 있습니다.

* 송신자 또는 수신자의 평가 데이터 갱신
* 트랜잭션 허용 또는 차단 결정

<HTMLBlock>{`
<details class="custom-accordion">
  <summary>Example of Callback Request Body</summary>

  <pre><code class="language-json">
{
   "callbackType":"CHAINALYSIS_KYT_RESULT",
   "data":{
      "verificationUuid":"69a310e6-810f-4a31-83d1-bcdafccf5304",
      "riskAssessment":{
         "chainalysisKYT":{
            "requestId":"f7231c6f-f1e7-4ae7-b143-2c87cd38abe9",
            "counterpartyVaspId":"15952089931162059995",
            "apiType":"ATTEMPT",
            "userId":"15952089931162059995",
            "direction":"OUTGOING",
            "network":"ETHEREUM",
            "asset":"ETH",
            "amount":"1",
            "usdPrice":"1820.17",
            "outputAddress":"bb3fd383d1c5540e52ef0a7bcb9433375793aeaf",
            "timestamp":"2023-05-18T12:39:44.000Z",
            "externalId":"79382ac9-c7be-3fab-ad56-8c61c654e2fc",
            "status":"PROCESSED",
            "alertCount":1,
            "createdAt":"2023-05-18T12:39:46.000Z",
            "assessedAt":"2023-05-18T12:39:45.263Z"
         },
         "chainalysisKYTAlerts":[
            {
               "counterpartyVaspId":"15952089931162059995",
               "externalId":"79382ac9-c7be-3fab-ad56-8c61c654e2fc",
               "direction":"OUTGOING",
               "alertId":"118b8cc8-f579-11ed-b86d-a3210c6ca9b8",
               "alertLevel":"MEDIUM",
               "entityCategory":"high risk exchange",
               "serviceName":"HIGH RISK EXCHANGE: SimpleSwap.io bb3fd383d1c5540e52ef0a7bcb9433375793aeaf",
               "exposureType":"DIRECT",
               "alertAmount":"1820.17",
               "createdAt":"2023-05-18T12:39:52.461Z"
            }
         ]
      }
   }
}
  </code></pre>
</details>
`}</HTMLBlock>

#### 6. REFINITIV\_WCO\_RESULT 유형 콜백 처리 (선택사항)

콜백으로 수신한 Refinitiv WCO 리스크 평가 결과에 따라 다음과 같은 작업을 수행할 수 있습니다.

* 송신자 또는 수신자의 평가 데이터 갱신
* 트랜잭션 허용, 재개 또는 차단 결정

<HTMLBlock>{`
<details class="custom-accordion">
  <summary>Example of Callback Request Body</summary>

  <pre><code class="language-json">
{
   "callbackType":"REFINITIV_WCO_RESULT",
   "data":{
      "verificationUuid":"69a310e6-810f-4a31-83d1-bcdafccf5304",
      "riskAssessment":{
         "refinitivWorldCheckOne":{
            "counterpartyVaspId":"15952089931162058999",
            "direction":"INCOMING",
            "caseSystemId":"5jb7r2c9xjfk1hoc95gfayv6m",
            "status":"PROCESSED",
            "matchStrength":"EXACT",
            "aggregatedSummaryResult":"{\"caseId\":\"69a310e6-810f-4a31-83d1-bcdafccf5304-INCOMING-1684413585757\", ... }}}",
            "createdAt":"2023-05-18T12:39:48.000Z",
            "assessedAt":"2023-05-18T12:39:57.834Z"
         }
      }
   }
}
  </code></pre>
</details>
`}</HTMLBlock>

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