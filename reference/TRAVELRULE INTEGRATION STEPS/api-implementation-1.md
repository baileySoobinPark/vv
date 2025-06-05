---
title: API Implementation
excerpt: >-
  TravelRule 연동을 위한 첫번째 단계인 VASP API 구현 단계입니다. 본 문서를 통해 VASP 백엔드에 구현해야 하는 REST
  API의 명세와 요구사항을 확인할 수 있습니다.
deprecated: false
hidden: false
metadata:
  robots: index
---
VASP API는 TravelRule 프로토콜 준수를 위한 핵심 구현 요소로, 각 VASP의 정책과 데이터를 기반으로 검증 및 입출금 관리에 필요한 비즈니스 로직을 실행하는 역할을 합니다. VASP 백엔드에 구현된 VASP API들은 Enclave 서버로부터 호출되어 계정 및 사용자 정보 검증, 트랜잭션 상태 조회, 결과 보고 수신 등의 주요 기능을 담당함으로서 규제 요구사항을 충족하고 프로토콜 흐름을 완성합니다.

본 문서에서는 구현이 필요한 API 목록과 각 API의 동작 목적, 호출 흐름, 구현 시 유의사항 등을 순차적으로 설명합니다.

<br />

## 구현 대상 VASP API 목록

각 VASP는 자산 전송 시 송신 VASP와 수신 VASP의 역할을 모두 수행해야 합니다. 이를 위해 VASP가 필수 구현해야 하는 API 목록과 해당 API가 호출되는 시점의 VASP의 포지션(송신 또는 수신 VASP), 그리고 해당 API에서 수행해야 하는 주요 비즈니스 로직을 아래 표에서 확인할 수 있습니다. 각 API의 구체적인 구현 요구사항 및 명세는 각 API Specficiation 문서를 확인하세요.

<HTMLBlock>{`
<style>
  .api-table {
    width: 100%;
    border-collapse: collapse;
    background-color: #fff;
    font-size: 14px;
    margin-top: 24px;
  }

  .api-table th, .api-table td {
    border: 1px solid #ddd;
    padding: 12px 14px;
    vertical-align: top;
    text-align: left;
    background-color: #fff;
    min-width: 220px;
  }

	.api-table th {
    background-color: #f8f9fa;
    color: #333;
    font-weight: bold;
  }

  .api-name a {
    color: #1364FF;
    text-decoration: none;
  }

  .api-name a:hover {
    text-decoration: underline;
  }

  .api-role {
    color: #555;
    font-weight: 500;
  }

  .callback-events code {
    background-color: #f4f4f4;
    padding: 2px 6px;
    border-radius: 4px;
    font-family: monospace;
    font-size: 13px;
    display: inline-block;
    margin: 2px 0;
  }

  .badge-key {
    display: inline-block;
    font-size: 11px;
    font-weight: 500;
    color: #fff;
    background-color: #1364FF;
    padding: 2px 6px;
    border-radius: 4px;
    margin-bottom: 6px;
  }
</style>

<table class="api-table">
  <thead>
    <tr>
      <th>API 이름</th>
      <th>VASP 포지션</th>
      <th>API의 역할</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="api-name"><a href="#">Verify User Account API</a></td>
      <td class="api-role">수신 VASP</td>
      <td>수신 계정이 VASP에서 발급된 계정인지 여부를 검증합니다.</td>
    </tr>
    <tr>
      <td class="api-name"><a href="#">Verify User API</a></td>
      <td class="api-role">수신 VASP</td>
      <td>
        <span class="badge-key">핵심 API</span><br>
        VASP의 자체 KYC/AML 데이터 및 정책을 기준으로 송신자가 입력한 수신자 정보를 검증합니다.
        검증 결과를 반환하여 해당 자산 전송건을 허용 또는 반려(Deny)할 수 있습니다.
      </td>
    </tr>
    <tr>
      <td class="api-name"><a href="#">Callback API</a></td>
      <td class="api-role">송신 VASP & 수신 VASP</td>
      <td>
        Enclave와의 비동기 통신을 위해 제공해야 하는 공통 인터페이스입니다. 아래 5개 타입의 Callback 이벤트를 수신할 수 있습니다.
        <div class="callback-events">
          <code>VERIFICATION_RESULT</code> : 검증 결과 수신<br>
          <code>TX_REPORT</code> : 트랜잭션 결과 수신<br>
          <code>ERROR_REPORT</code> : 프로토콜 처리 중 에러 수신<br>
          <code>CHAINALYSIS_KYT_RESULT</code> : Chainalysis 연계 리스크 평가 결과 수신<br>
          <code>REFINITIV_WCO_RESULT</code> : Refinitiv WCO 연계 리스크 평가 결과 수신
        </div>
      </td>
    </tr>
    <tr>
      <td class="api-name"><a href="#">Check Transaction Status API</a></td>
      <td class="api-role">송신 VASP</td>
      <td>온체인 송금 트랜잭션의 현재 처리 상태를 조회하여 반환합니다.</td>
    </tr>
    <tr>
      <td class="api-name"><a href="#">Database Management API</a></td>
      <td class="api-role">송신 VASP & 수신 VASP</td>
      <td>Enclave 데이터베이스에 사용할 암호화 키를 반환하여 Runtime으로 주입합니다.</td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

<br />

## VASP API 인증 구현 (선택)

인증 기능 구현을 통해 VASP API를 오직 Enclave만 호출할 수 있도록 접근을 제한하고 보안을 강화할 수 있습니다. 적절한 인증 헤더를 정의하고 전달된 인증 토큰을 검증하는 로직을 추가 구현한 뒤 Enclave 환경 변수를 설정하여 모든 Enclave 발 VASP API 요청이 인증 헤더를 포함하도록 설정하세요.

설정 방법은 다음과 같습니다.

#### 인증 관련 Enclave 환경 변수

* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: 인증 토큰 값(Value). 설정한 값이 Enclave의 모든 요청의 인증 헤더 값으로 포함되어 전달됩니다.
* `VEGA_VERIFICATION_AUTHORIZATION_KEY`: 인증 토큰을 전달할 HTTP 헤더의 Key.
  * 미 설정시 기본 `Authorization` 헤더를 사용합니다.

**예시**

```json
// VEGA_VERIFICATION_AUTHORIZATION_KEY 미설정 시 헤더 예시
Authorization: Bearer <VEGA_VERIFICATION_AUTHORIZATION_TOKEN>

// VEGA_VERIFICATION_AUTHORIZATION_KEY를 X-Api-Key 로 설정 시 헤더 예시
X-Api-Key: <VEGA_VERIFICATION_AUTHORIZATION_TOKEN>
```