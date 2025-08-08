---
title: VerifyName API
api:
  file: VN_VASP_API_Spec.yaml
  operationId: verifyName-Request-Verification
hidden: false
---
In the VerifyName protocol, a VASP plays the role of both the originating VASP and the beneficiary VASP.\
This API can be invoked in both roles. It verifies the validity of the transferred asset information and the beneficiary’s wallet address, and returns the account owner’s full name and date of birth as the response.

The returned personal information is used inside the Enclave to compare and validate against the originator’s information.\
All VASPs that support VerifyName must implement this API to handle ownership verification requests from other VASPs.

***

## Implementation Guide

### Functional Requirements

#### 1. Implement business logic for each scenario

The VerifyName protocol supports both pre-verification and post-verification based on the timing of the transaction.\
The VASP must perform the appropriate verification logic according to the scenario, return the user information in the response, and deliver it to the Enclave.

**Post-Verification Requirements**

* Applies when the request `type` is **VerifyOriginator** and your VASP is acting as the **ordering VASP**. This occurs when the asset transfer transaction has already been executed by your VASP, and the **beneficiary VASP** requests verification afterward.
* Verify whether the `tx_hash` in the request matches a transaction executed by your VASP. Return the result in the `tx_hash` field inside the `verification_results` object in the response.
* Verify that the transaction’s beneficiary wallet address matches the `supplementary_data.envelope.address` provided in the request. Return the result in the `address` field of `verification_results`. If the request includes a `supplementary_data.envelope.tag`, also verify tag consistency and include it in the address field result.
* Additionally, verify whether other information such as ticker, network, and dti matches, and return the results in the respective fields of the `verification_results` object.
* If the originator can be identified, include their personal information in the `debtor` object in the response.

**Pre-Verification Requirements**

* 일치하는 주소가 존재하는 경우, 해당 주소의 소유주 정보를 `creditor` 객체에 포함하여 반환합니다.
* Applies when the request `type` is **VerifyBeneficiary** and your VASP is acting as the **beneficiary VASP**. This occurs when the **ordering VASP** requests verification before executing the asset transfer.
* Verify that the `supplementary_data.envelope.address` in the request matches one of your VASP’s registered beneficiary deposit addresses. Return the result in the `address` field of `verification_results`. If the request includes a `supplementary_data.envelope.tag`, also verify tag consistency and include it in the `address` field result.
* Additionally, verify whether other information such as **ticker**, **network**, and **dti** matches, and return the results in the respective fields of the `verification_results` object.
* If a matching address exists, include the address owner’s information in the `creditor` object in the response.

<br />

#### 2. Return verification results

* For each item verified, return the result in the relevant field of the `verification_results` object in the response.
* Each field must have one of the following values: **MATCHED**, **MISMATCHED**, or **SKIPPED**.
* Even if no value is provided in the request (and therefore verification is not performed), or if your VASP does not have the relevant information, you must still include the field in the result with the value **SKIPPED**. Do not omit or leave the field empty.
* This requirement applies to all optional items as well.

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
      <td>Pre-verification의 경우, 요청 address가 등록된 수취인 주소인지 확인<br>Post-verification의 경우, 해당 트랜잭션에서 사용된 수취인 주소와 비교<br>tag가 입력된 경우, tag 일치 여부도 결과에 반영해야 함<br>일치하면<code>MATCHED</code>, 다르면 <code>MISMATCHED</code></td>
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

#### 3. Return account owner information

If the verification result for the address or tx\_hash field is MATCHED, the account owner information related to that account must be included in the response.

The scope of information to be provided is as follows:

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

* `VerifyOriginator` → Return the originator account owner information in the `debtor` object.

<Accordion title="Example of Response Body: VerifyOriginator type – individual account" icon="fa-info-circle">
  ```json
  {
    "verification_results": {
      "ticker": "MATCHED",
      "network": "SKIPPED",
      "address": "MATCHED",
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
          },
          "local_name": {
            "first_name": "길동",
            "last_name": "홍"
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

<Accordion title="Example of Response Body: VerifyOriginator type – corporate account" icon="fa-info-circle">
  ```json
  {
    "verification_results": {
      "ticker": "MATCHED",
      "network": "SKIPPED",
      "address": "MATCHED",
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

* `VerifyBeneficiary` → Return the beneficiary account owner information in the creditor object.

<Accordion title="Example of Response Body: VerifyBeneficiary type – individual account" icon="fa-info-circle">
  ```json
  {
    "verification_results": {
      "ticker": "MATCHED",
      "network": "SKIPPED",
      "address": "MATCHED",
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
          },
          "local_name": {
            "first_name": "길동",
            "last_name": "홍"
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

<Accordion title="Example of Response Body: VerifyBeneficiary type – corporate account" icon="fa-info-circle">
  ```json
  {
    "verification_results": {
      "ticker": "MATCHED",
      "network": "SKIPPED",
      "address": "MATCHED",
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

### Constraints

This API must return a response within 3 seconds.

### Environment Variable Settings

To ensure proper integration with the Enclave, configure the following Enclave environment variables:

* `VEGA_VERIFICATION_VERIFY_NAME_V2_API_PATH`: Path of this API
* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: Authentication token value for API authentication
* `VEGA_VERIFICATION_AUTHORIZATION_KEY`: Header key used to pass the authentication token

***

## API Specification