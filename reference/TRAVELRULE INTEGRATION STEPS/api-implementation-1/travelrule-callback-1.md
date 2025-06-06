---
title: Callback API
api:
  file: TR_VASP_API_KR_Spec.yaml
  operationId: travelrule-callback
hidden: false
---
VASP는 TravelRule 프로토콜 내에서 송신 VASP와 수신 VASP의 역할을 모두 수행해야 합니다. 이 API는 두 역할 모두에서 비동기적 콜백 상황을 처리하기 위한 공통 인터페이스입니다. Enclave는 상대 VASP로부터 Report API가 호출되었을 때 이 API를 실행합니다.

***

## 구현 가이드

### 기능 요구사항

#### 1. 콜백 타입 분기 처리

요청의 `callbackType` 필드에 따라 각 콜백 유형에 맞는 비즈니스 로직으로 분기 처리해야 합니다. 지원되는 콜백 타입은 아래와 같으며, `VERIFICATION_RESULT`, `TX_REPORT`, `ERROR_REPORT`는 필수 구현 대상입니다.

<HTMLBlock>{`
<HTMLBlock>{\`
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
      <td class="code-col"><code>VERIFICATION_RESULT</code></td>
      <td>송신 VASP 역할에서 사용됩니다. 수신자 검증이 종료되어 결과가 비동기적으로 전달될 때 호출됩니다.</td>
    </tr>
    <tr>
      <td class="code-col"><code>TX_REPORT</code></td>
      <td>수신 VASP 역할에서 사용됩니다. 송신 VASP가 트랜잭션 결과를 Report할 때 호출됩니다.</td>
    </tr>
    <tr>
      <td class="code-col"><code>ERROR_REPORT</code></td>
      <td>수신 VASP 역할에서 사용됩니다. 송신 VASP가 오류를 Report할 때 호출됩니다.</td>
    </tr>
    <tr>
      <td class="code-col"><code>CHAINALYSIS_KYT_RESULT</code></td>
      <td>Chainalysis KYT 결과를 비동기 방식으로 전달할 때 호출됩니다. 선택적으로 구현 가능합니다.</td>
    </tr>
    <tr>
      <td class="code-col"><code>REFINITIV_WCO_RESULT</code></td>
      <td>Refinitiv WCO 결과를 비동기 방식으로 전달할 때 호출됩니다. 선택적으로 구현 가능합니다.</td>
    </tr>
  </tbody>
</table>
\`}</HTMLBlock>
`}</HTMLBlock>

#### 2. VERIFICATION\_RESULT 처리

콜백으로 수신한 검증 결과에 따라 후속 조치를 수행해야 합니다.

* 검증 성공 시, 이어서 송신 VASP측 수신자 검증을 진행하거나 트랜잭션을 실행합니다.
* 검증 실패 시, 자산의 출금을 취소로 처리하고 사용자에게 적절한 안내 메세지와 함께 전송 실패를 고지합니다. `data.reason`필드로부터 실패 사유를 참조하여 안내 메세지에 반영할 수 있습니다.

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
      <td class="code-col"><code>VERIFICATION_RESULT</code></td>
      <td>송신 VASP 역할에서 사용됩니다. 수신자 검증이 종료되어 결과가 비동기적으로 전달될 때 호출됩니다.</td>
    </tr>
    <tr>
      <td class="code-col"><code>TX_REPORT</code></td>
      <td>수신 VASP 역할에서 사용됩니다. 송신 VASP가 트랜잭션 결과를 보고할 때 호출됩니다.</td>
    </tr>
    <tr>
      <td class="code-col"><code>ERROR_REPORT</code></td>
      <td>수신 VASP 역할에서 사용됩니다. 송신 VASP가 오류를 보고할 때 호출됩니다.</td>
    </tr>
    <tr>
      <td class="code-col"><code>CHAINALYSIS_KYT_RESULT</code></td>
      <td>Chainalysis KYT 결과가 도착했을 때 호출됩니다. 선택적으로 구현 가능합니다.</td>
    </tr>
    <tr>
      <td class="code-col"><code>REFINITIV_WCO_RESULT</code></td>
      <td>Refinitiv WCO 결과가 도착했을 때 호출됩니다. 선택적으로 구현 가능합니다.</td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

#### 3. TX\_REPORT 처리 (수신 VASP 역할)

콜백으로 수신한 트랜잭션 Hash가 수신자의 실제 입금 주소로 발생한 트랜잭션인지 확인하고 결과 및 이력을 데이터베이스에 기록합니다.

#### 4. ERROR\_REPORT 처리 (수신 VASP 역할)

오류 보고 내용을 확인한 뒤 해당 전송을 취소한 뒤, 트랜잭션 추적을 중단하고 로그 기록을 남깁니다.

#### 5. CHAINALYSIS\_KYT\_RESULT 처리

콜백으로 수신한 Chainalysis KYT 리스크 평가 결과에 따라 다음과 같은 작업을 수행할 수 있습니다.

* Originator 또는 Beneficiary의 평가 데이터 갱신
* 트랜잭션 허용 또는 차단 결정

#### 6. REFINITIV\_WCO\_RESULT 처리

콜백으로 수신한 Refinitiv WCO 리스크 평가 결과에 따라 다음과 같은 작업을 수행할 수 있습니다.

* Originator 또는 Beneficiary의 평가 데이터 갱신
* 트랜잭션 허용, 재개 또는 차단 결정

### Functional Requirements

#### 1. 콜백 타입별 처리 로직 분기

콜백 요청의 `callbackType` 필드에 따라 적절한 비즈니스 로직으로 분기 처리해야 합니다. 다음은 지원해야 하는 콜백 타입입니다.

* `VERIFICATION_RESULT`: Originating VASP 역할일 때, 사용자 검증 결과가 비동기로 전달됨

* `TX_REPORT`: Beneficiary VASP 역할일 때, Originating VASP가 트랜잭션 실행 결과를 전달함

* `ERROR_REPORT`: Beneficiary VASP 역할일 때, Originating VASP가 오류 보고를 전달함\
  → 위 세 가지는 **필수 구현 대상입니다.**

* `CHAINALYSIS_KYT_RESULT`: Chainalysis KYT 결과가 도착했을 때

* `REFINITIV_WCO_RESULT`: Refinitiv WCO 결과가 도착했을 때\
  → 위 두 가지는 해당 리스크 평가 API를 사용하는 경우에만 **선택적 구현 대상입니다.**

#### 2. VERIFICATION\_RESULT (Originating VASP)

Originator 사용자에 대한 검증 결과가 도착하면, 해당 결과에 따라 다음 작업을 수행해야 합니다.

* 검증 성공 시: 수신자 검증을 이어서 진행하거나, 트랜잭션 실행 진행
* 검증 실패 시: 자산 출금 취소 처리 및 사용자에게 실패 사유 전송 (`data.reason` 활용)

#### 3. TX\_REPORT (Beneficiary VASP)

수신자가 실제로 입금을 받았는지 확인하기 위해 다음을 수행할 수 있습니다.

* 보고된 트랜잭션 해시와 수신 지갑 입금 정보 매칭
* 트랜잭션 수신 확인 및 기록 보관

#### 4. ERROR\_REPORT (Beneficiary VASP)

오류가 보고된 경우 다음과 같은 처리를 수행할 수 있습니다.

* 관련 자산 출금 요청 취소
* 트랜잭션 추적 중지 및 로그 기록

#### 5. CHAINALYSIS\_KYT\_RESULT

Chainalysis KYT 분석 결과 도착 시 다음과 같이 활용합니다.

* 결과를 기반으로 Originator 또는 Beneficiary의 평가 데이터 갱신
* 분석 결과에 따라 트랜잭션 허용 또는 차단 결정

#### 6. REFINITIV\_WCO\_RESULT

Refinitiv WCO 분석 결과 도착 시 다음과 같이 활용합니다.

* 결과를 기반으로 Originator 또는 Beneficiary의 평가 데이터 갱신
* 트랜잭션 허용, 재개 또는 차단 결정

***

## Reason Codes for `DENIED` and `ERROR` Results

아래 테이블은 `VERIFICATION_RESULT` 콜백에서 `DENIED` 또는 `ERROR` 결과가 발생했을 때 사용하는 사유 코드입니다.

| Reason                            | Result | Message 예시                                                     | 설명                                                    |
| --------------------------------- | ------ | -------------------------------------------------------------- | ----------------------------------------------------- |
| `UNKNOWN-SYMBOL`                  | DENIED | `"ETH"`                                                        | Originating VASP가 제공한 가상자산 종목이 수신 VASP에서 지원되지 않을 때    |
| `UNKNOWN-NETWORK`                 | DENIED | `"Ethereum"`                                                   | 네트워크 정보가 부족하거나 수신 VASP에서 해당 네트워크를 지원하지 않을 때           |
| `UNKNOWN-ADDRESS`                 | DENIED | `"0x... is not registered."`                                   | 수신 VASP가 해당 주소를 관리하지 않을 때                             |
| `LACK-OF-INFORMATION`             | DENIED | `"ACCOUNT_NUMBER"`                                             | 수신 VASP가 검증 수행에 필요한 Originator 정보가 부족한 경우             |
| `UNAVAILABLE-INFORMATION`         | DENIED | `"ACCOUNT_NUMBER"`                                             | Originating VASP가 요청한 정보를 수신 VASP가 보유하지 않거나 제공 불가능할 때 |
| `BLACKLISTED`                     | DENIED | `"0x.. is listed on the blacklist."`                           | Originator가 제재 목록에 포함되어 리스크 판단 결과 거절된 경우              |
| `UNVERIFIED-KYC`                  | DENIED | `"0x.. is unverified KYC"`                                     | 수신자가 KYC 미완료 상태일 때                                    |
| `MISMATCHED-NAME`                 | DENIED | `"Name is not matched."`                                       | 수신자의 이름이 Originator가 제공한 정보와 일치하지 않을 때                |
| `NOT-ALLOWED`                     | DENIED | `"This user is locked by internal policy."`                    | 내부 정책에 따라 수신자가 차단된 경우                                 |
| `UNDEFINED-ERROR`                 | DENIED | `"Undefined Error is occurred."`                               | 정의되지 않은 오류가 발생한 경우                                    |
| `BENEFICIARY-ACCOUNT-NOT-MATCHED` | ERROR  | `"Beneficiary account is not matched with requested account."` | 수신 VASP가 요청된 주소와 다른 주소를 반환한 경우                        |
| `REQUEST-TIMEOUT`                 | ERROR  | `"Request timeout."`                                           | 검증 요청이 허용된 시간 내에 완료되지 않은 경우                           |

***

### 제약 조건

* 이 API는 1초 이내에 응답해야 합니다.
* 응답의 HTTP 상태 코드는 200 OK만 허용됩니다.
* 동일한 콜백 요청이 여러 번 수신되어도 처리 결과가 동일하도록 멱등성을 보장해야 합니다.\
  (ex) 중복 요청 시 내부 처리 로직에서 무시하도록 구현

***

### 구현 권장사항

* 콜백 API의 경우 응답 속도가 중요하므로, 시간 소모가 큰 작업은 응답 이후 비동기 방식으로 처리하는 것을 권장합니다.

***

### Enclave 연동 설정

Enclave와의 정상 연동을 위해 아래 환경 변수를 설정해야 합니다.

* `VEGA_VERIFICATION_CALLBACK_API_PATH`: 해당 API 경로
* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: VerifyVASP 온보딩 시 발급받은 API Key

***

## API 명세