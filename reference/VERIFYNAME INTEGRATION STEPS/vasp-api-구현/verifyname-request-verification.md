---
title: VerifyName API
api:
  file: VN_VASP_API_Spec.yaml
  operationId: verifyName-Request-Verification
hidden: false
---
VASP는 VerifyName 프로토콜 내에서 송신 VASP와 수신 VASP의 역할을 모두 수행합니다. 본 API는 수신 VASP와 송신 VASP 역할 수행시 모두 호출될 수 있는 API입니다. 전송 자산 정보와 수신인 지갑 주소의 유효성을 검증하고, 해당 주소 소유자의 성명과 생년월일 값을 응답으로 반환합니다. 반환된 개인 정보는 Enclave 내부에서 송신자 정보와의 비교 검증에 사용됩니다. VerifyName을 지원하는 모든 VASP는 반드시 이 API를 구현하여 타 VASP들로부터의 소유자 검증 요청에 대응해야 합니다.

***

## 구현 가이드

### 기능 요구사항

#### 1. 시나리오별 비즈니스 로직 구현

VerifyName 프로토콜은 트랜잭션 전송 시점을 기준으로 사전 검증과 사후 검증을 모두 지원합니다. VASP는 시나리오에 따라 적절한 검증 로직을 수행하고 사용자 정보를 응답에 반환하여 Enclave로 전달해야 합니다.

**사후 검증(Post-Verification) 구현 요구사항**

* 요청 type이 `VerifyOriginator`인 경우로, 귀사의 VASP는 송신 VASP로서 검증 요청에 응답해야 합니다. 송신 VASP로부터 자산 이전 트랜잭션이 먼저 실행되어, 수신 VASP가 검증을 요청한 경우입니다.
* 요청에 포함된 tx\_hash 값이 귀사의 VASP가 실행한 트랜잭션이 맞는지 검증하고, 결과를 응답의 `verification_results` 객체 내 `tx_hash` 필드로 반환해야 합니다.
* 해당 트랜잭션의 수신인 지갑 주소가 API 요청에 포함된 `supplementary_data.envelope.address`의 주소와 일치하는지 검증하고, 결과를 응답 객체 `verification_results`의 `address` 필드에 반환해야 합니다. 또한 요청에`supplementary_data.envelope.tag` 정보가 포함되어 있는 경우, tag 일치 여부도 포함하여 `address` 필드의 결과에 반영해야 합니다.
* 그 밖에 `ticker`, `network`, `dti` 등의 정보가 일치하는지 검증하고, 결과를 `verification_results` 객체 내 각 필드로 반환해야 합니다.
* 송신자를 특정할 수 있는 경우, 해당 송신자의 개인 정보를 `debtor` 객체에 포함하여 반환합니다.

**사전 검증(Pre-Verification) 구현 요구사항**

* 요청 type이 `VerifyBeneficiary`인 경우로, 귀사의 VASP는 수신 VASP로서 검증 요청에 응답해야 합니다. 송신 VASP가 자산 이전을 실행하기에 앞서 수신 VASP로 검증을 요청한 경우입니다.
* 요청의 `supplementary_data.envelope.address`의 주소가 귀사의 VASP에 등록된 수취인 입금 주소 중 하나와 일치하는지 확인하고, 결과를 응답 객체 `verification_results`의 `address` 필드에 반환해야 합니다. 또한 요청에`supplementary_data.envelope.tag` 정보가 포함되어 있는 경우, tag 일치 여부도 포함하여 `address` 필드의 결과에 반영해야 합니다.
* 그 밖에 해당 주소의 `ticker`, `network`, `dti` 등의 정보가 일치하는지 검증하고, 결과를 `verification_results` 객체 내 각 필드로 반환해야 합니다.
* 일치하는 주소가 존재하는 경우, 해당 주소의 소유주 정보를 `creditor` 객체에 포함하여 반환합니다.

<br />

#### 2. 검증 수행 결과 반환

항목별 검증 수행 결과를 응답의 `verification_results` 객체 내 관련 필드에 명시하여 반환해야 합니다. 각 필드는 `MATCHED`, `MISMATCHED`, `SKIPPED` 중 하나의 값을 가질수 있습니다. **요청에 값이 입력되지 않아서 검증을 수행하지 않았거나, VASP에 해당 정보가 없어서 검증을 수행하지 못한 항목의 경우라도 빈 값으로 반환하거나 제외하지 않고`SKIPPED`로 반드시 항목을 포함하여 반환해야**합니다. 즉, 아래의 Optional 항목들도 모두 검증 결과에 포함되어야 합니다. 각 항목별 결과는 다음과 같은 정책에 따라 결정할 수 있습니다.

<HTMLBlock>{`
<table class="verify-params">
  <thead>
    <tr>
      <th>필드 명</th>
      <th width=110px>검증 필수여부</th>
      <th>결과 반환 정책</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>address</code></td>
      <td>Required</td>
      <td>Pre-verification의 경우, 요청 address가 등록된 수취인 주소인지 확인<br>Post-verification의 경우, 해당 트랜잭션에서 사용된 수취인 주소와 비교<br>tag가 입력된 경우, tag 일치 여부도 검사하여 결과에 반영해야 함<br><code>MATCHED</code>, 다르면 <code>MISMATCHED</code></td>
    </tr>
    <tr>
      <td><code>tx_hash</code></td>
      <td>Optional</td>
      <td>요청 tx_hash 값이 귀사의 VASP가 실행한 트랜잭션인지 확인하여 일치하면 <code>MATCHED</code>, 다르면 <code>MISMATCHED</code><br>검증 항목이 아니거나 값이 제공되지 않은 경우 <code>SKIPPED</code></td>
    </tr>
    <tr>
      <td><code>ticker</code></td>
      <td>Required</td>
      <td>앞서 찾은 address 혹은 tx_hash가 해당 자산에 관한 것이면 <code>MATCHED</code>, 그렇지 않으면 <code>MISMATCHED</code></td>
    </tr>
    <tr>
      <td><code>network</code></td>
      <td>Optional</td>
      <td>앞서 찾은 address 혹은 tx_hash가 해당 네트워크에 관한 것이면 <code>MATCHED</code>, 그렇지 않으면 <code>MISMATCHED</code><br>검증 미요청 항목이거나 비교할 수 없는 경우 <code>SKIPPED</code></td>
    </tr>
    <tr>
      <td><code>dti</code></td>
      <td>Optional</td>
      <td>앞서 찾은 address 혹은 tx_hash가 해당 디지털 자산 식별자(DTI)에 관한 것이면 <code>MATCHED</code>, 아니면 <code>MISMATCHED</code><br>검증 미요청 항목이거나 비교할 수 없는 경우 <code>SKIPPED</code></td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

<br />

#### 3. 주소 소유주 정보 반환

`address` 또는 `tx_hash` 필드의 검증 결과가 `MATCHED`인 경우, 관련 계정의 소유주 정보를 응답에 포함하여 반환해야 합니다. 정보 제공 범위는 다음과 같습니다.

<HTMLBlock>{`
<style>
.personal-info-table {
  width: 100%;
  border-collapse: collapse;
  background-color: white;
  font-size: 14px;
}

.personal-info-table th,
.personal-info-table td {
  border: 1px solid #ccc;
  padding: 10px;
  text-align: left;
  vertical-align: top;
}

.personal-info-table thead {
  background-color: white;
  font-weight: bold;
}  
</style>
<table class="personal-info-table">
  <thead>
    <tr>
      <th>계정 유형</th>
      <th>개인정보 제공 범위</th>
      <th>필수 여부</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="2">개인</td>
      <td>이름</td>
      <td>필수</td>
    </tr>
    <tr>
      <td>생년월일<small>(ex)<code>2025-01-01</code></small></td>
      <td>필수</td>
    </tr>
    <tr>
      <td rowspan="5">법인</td>
      <td>이름</td>
      <td>필수</td>
    </tr>
    <tr>
      <td>법인 설립일 <small>(ex)<code>2025-01-01</code></small></td>
      <td>필수</td>
    </tr>
    <tr>
      <td>LEI</td>
      <td>선택</td>
    </tr>
    <tr>
      <td>BIC</td>
      <td>선택</td>
    </tr>
    <tr>
      <td>Identification &amp; Issuer</td>
      <td>선택</td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

검증 요청 `type` 에 따른 소유주 정보 응답 예시는 다음과 같습니다.

* `VerifyOriginator` 인 경우 트랜잭션 송신 계좌의 소유주 정보를 `debtor`객체로 반환합니다.

<Accordion title="Example of Response Body: VerifyOriginator 타입, 개인 계정인 경우" icon="fa-info-circle">
  ```json
  {
    "verification_results": {
      "ticker": "MATCHED",
      "network": "SKIPPED",
      "address": "MATCHED",
      "tag": "SKIPPED",
      "tx_hash": "MATCHED",
      "dti": "SKIPPED"
    },
    "debtor": {
      "name": "HONG KIL DONG",
      "supplementary_data": {
        "envelope": {
          "name": {
            "first_name": "GIL DONG",
            "last_name": "HONG"
          }
        }
      },
      "identification": {
        "private_identification": {
          "date_and_place_of_birth": {
            "birth_date": "2025-01-01"
          }
        }
      }
    }
  }
    
  ```
</Accordion>

<Accordion title="Example of Response Body: VerifyOriginator 타입, 법인 계정인 경우" icon="fa-info-circle">
  ```json
  {
    "verification_results": {
      "ticker": "MATCHED",
      "network": "SKIPPED",
      "address": "MATCHED",
      "tag": "SKIPPED",
      "tx_hash": "MATCHED",
      "dti": "SKIPPED"
    },
    "debtor": {
      "name": "Apple",
      "identification": {
        "organisation_identification": {
          "supplementary_data": {
            "envelope": {
              "date_of_incorporation": "2020-01-01"
            }
          }
        },
        "lei": "506700GE1G29325QX363",
        "bic": "KRKRKR",
        "other": {
          "identification": "5493001KJTIIGC8Y1R12",
          "issuer": "ISO17442"
        }
      }
    }
  }

  ```
</Accordion>

* `VerifyBeneficiary` 인 경우 수신 계좌의 소유주 정보를 `creditor`객체로 반환합니다.

<Accordion title="Example of Response Body: VerifyBeneficiary 타입, 개인 계정인 경우" icon="fa-info-circle">
  ```json
  {
    "verification_results": {
      "ticker": "MATCHED",
      "network": "SKIPPED",
      "address": "MATCHED",
      "tag": "SKIPPED",
      "tx_hash": "SKIPPED",
      "dti": "SKIPPED"
    },
    "creditor": {
      "name": "HONG KIL DONG",
      "supplementary_data": {
        "envelope": {
          "name": {
            "first_name": "GIL DONG",
            "last_name": "HONG"
          }
        }
      },
      "identification": {
        "private_identification": {
          "date_and_place_of_birth": {
            "birth_date": "2025-01-01"
          }
        }
      }
    }
  }
  ```
</Accordion>

<Accordion title="Example of Response Body: VerifyBeneficiary 타입, 법인 계정인 경우" icon="fa-info-circle">
  ```json
  {
    "verification_results": {
      "ticker": "MATCHED",
      "network": "SKIPPED",
      "address": "MATCHED",
      "tag": "MATCHED",
      "tx_hash": "SKIPPED",
      "dti": "SKIPPED"
    },
    "creditor": {
      "name": "Apple",
      "identification": {
        "organisation_identification": {
          "supplementary_data": {
            "envelope": {
              "date_of_incorporation": "2020-01-01"
            }
          }
        },
        "lei": "506700GE1G29325QX363",
        "bic": "KRKRKR",
        "other": {
          "identification": "5493001KJTIIGC8Y1R12",
          "issuer": "ISO17442"
        }
      }
    }
  }
  ```
</Accordion>

<br />

### 제약 사항

이 API는 3초 이내에 응답해야 합니다.

### 환경 변수 설정

Enclave와의 정상 연동을 위해 아래와 같이 Enclave 환경 변수를 설정해야합니다.

* `VEGA_VERIFICATION_VERIFY_NAME_V2_API_PATH`: 해당 API의 경로
* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: API 인증을 위한 인증 토큰 값
* `VEGA_VERIFICATION_AUTHORIZATION_KEY`: API 인증 토큰을 전달할 header key

***

## API 명세