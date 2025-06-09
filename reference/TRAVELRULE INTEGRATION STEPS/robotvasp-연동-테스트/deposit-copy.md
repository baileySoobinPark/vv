---
title: 입금 시나리오 테스트
excerpt: 본 문서 가이드를 통해 입금 시나리오를 확인하고 테스트 케이스와 기대 동작을 확인할 수 있습니다.
deprecated: false
hidden: false
metadata:
  robots: index
---
> 💡 Please Note:
>
> 1. 입금 테스트는 **Ethereum Sepolia** 및 **Ripple Testnet**에서만 수행 가능합니다. 테스트를 시작하기에 앞서, 해당 환경에서 자산 전송 테스트가 가능한지 여부와 VASP 입금 계좌 등 필요한 정보를 미리 확인하십시오.
> 2. 입금 테스트를 진행하기 위해 출금 테스트가 선행되어야 합니다. 출금 테스트를 진행하지 않은 사용자는 [출금 테스트]()를 먼저 진행한 후 입금 테스트를 진행해 주십시오.
> 3. 입금 테스트에서 귀사의 VASP로 입금될 자산의 총량은 출금 테스트에서 Robot VASP에게 전송한 가상 자산의 총량을 초과할 수 없습니다.

## 테스트 순서

입금 시나리오 테스트는 다음 같은 순서로 진행합니다.

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
      <div class="vasp-step-title">User Account Verification: 테스트용 수신 계정 정보에 대한 검증 요청을 Robot VASP에게 전송한 뒤, 기대 응답의 발생 여부를 확인합니다.</div>
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">2</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">User Verification: 테스트용 수신자 정보에 대한 검증 요청을 Robot VASP에게 전송한 뒤, 기대 응답의 발생 여부를 확인합니다.</div>
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">3-1</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">검증 이후 자산 전송 트랜잭션을 실행하는 시나리오로 진행합니다. 아래 두 하위 케이스에 대해 테스트합니다.</div>
      (Case 1)트랜잭션 Report 전송 (Case 2)트랜잭션 Report 미전송
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">3-2</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">검증 이후 트랜잭션을 취소하는 시나리오로 진행합니다. 아래 두 하위 케이스에 대해 테스트합니다. </div>
      (Case 1)Error Report 전송 (Case 2)Error Report 미전송
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">4</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">트랜잭션의 상태를 조회하는 시나리오로 진행합니다.  </div>
    </div>
  </li>
</ol>
`}</HTMLBlock>

<br />

## 테스트 데이터

테스트의 사용할 가상의 사용자 정보는 아래와 같습니다. 이 정보는 수신 VASP로 동작하는 Robot VASP에 미리 저장되어 있어 검증이 가능한 정보입니다.

<Accordion title="User 1(KYC 인증을 완료한 개인 사용자)">
  아래의 정보를 정확하게 입력하였다면 Robot VASP가 VERIFIED를 반환합니다.

  ### 개인 정보

  * **성**: Robbins

  * **이름**: Taylor

  ### 지갑 주소

  **'ETH' address**: `0xFa230E9cCAF5e382539147294d7965Eeccbbfa5c`

  **'XRP' address**: `rGFFufDwabHuPur9927p1EgBTcCBfsjtEU`

  **'XRP' destination tag**: `123456789`
</Accordion>

<Accordion title="User 2(KYC 인증을 완료하지 않은 개인 사용자)">
  아래의 정보를 정확하게 입력하여도 Robot VASP가 DENIED를 반환합니다.

  ### 개인 정보

  * **성**: Cook

  * **이름**: Ethan

  ### 지갑 주소

  **'ETH' address**: `0x319E92715729c46869ed31d228f3b4f31e951450`

  **'XRP' address**: `rGFFufDwabHuPur9927p1EgBTcCBfsjtEU`

  **'XRP' destination tag**: `345678912`
</Accordion>

<Accordion title="User 3(KYC 인증을 완료한 기업 사용자)">
  대표 이름을 포함한 모든 정보를 정확하게 입력한 경우 Robot VASP가 'VERIFIED'를 반환합니다. 그러나 다른 모든 정보를 정확하게 입력하였어도 대표 이름 정보에 오류가 있는 경우, DENIED를 반환합니다.

  ### 개인 정보

  * **기업 이름**: Garrit Studio

    ### 대표 이름

  * **성**: Clarke

  * **이름**: Jason

  ### 지갑 주소

  **'ETH' address**: `0x26704Dc20d0ddF6cAa45b4D2b8AcB643015B951E`

  **'XRP' address**: `rGFFufDwabHuPur9927p1EgBTcCBfsjtEU`

  **'XRP' destination tag**: `234567891`
</Accordion>

<br />

## Test Cases

### 1. User Account Verification Test

* **조건**

  * Robot VASP가 귀사의 VASP가 구현한 VASP API 중 User Account Verification API을 호출해야 합니다. User Account Verification Simulation API를 호출해 User Account Verification API를 호출하도록 명령할 수 있습니다.

  <br />

  <Accordion title="User Account Verification Simulation API 호출 방법">
    **Method**: `POST`

    * **Endpoint**: `https://api.verifyvasp.xyz/vega/robot/v1.0/action/verifications/account`

    * **요청 Body 예제**

    ```
    {
    "keyType": "PerVasp",
    "beneficiaryVaspId": "16384656509591635927", // your VASP ID
    "symbol": "ETH",
    "payload": {
      "version": "1.0",
      "ivms101": {
        "beneficiary": {
          "beneficiaryPersons": [
            {
              "naturalPerson": {
                "name": {
                  "nameIdentifier": [
                    {
                      "primaryIdentifier": "last name",
                      "secondaryIdentifier": "first name",
                      "nameIdentifierType": "LEGL"
                    }
                  ]
                }
              }
            }
          ],
          "accountNumber": ["0xb0bFf9721871e22653358956cf59a5FdBF3D752F"]
        }
      }
    }
    }
    ```
  </Accordion>
* **기대 결과**
  * 테스트 데이터를 기준으로 정상 검증(VERIFIED) 시나리오를 포함하여 발생 가능한 아래 모든 DENIED 케이스에 대해 기대한 사유 코드를 반환해야 합니다.
    * `VERIFIED`
    * `UNKNOWN-SYMBOL`
    * `UNKNOWN-ADDRESS`
    * `UNVERIFIED-KYC`
    * `UNAVAILABLE-INFORMATION`
    * `LACK-OF-INFORMATION`
    * `BLACKLISTED`

<br />

### 2. User Verification Test

* **조건**

  * Robot VASP가 귀사의 VASP가 구현한 VASP API 중 User Verification API을 호출해야 합니다. User Verification Simulation API를 호출해 User Verification API를 호출하도록 명령할 수 있습니다.
  * Robot VASP는 User Verification Simulation API를 실행하기 전 User Account Verification Simulation API를 호출해야 하며 응답으로 `VERIFIED`를 받은 후 User Verification API를 실행해야 합니다.

  <br />

  <Accordion title="User Verification Simulation API 호출 방법">
    **Method**: `POST`

    * **Endpoint**: `https://api.verifyvasp.xyz/vega/robot/v1.0/action/verifications`

    * **요청 Body 예제**

    ```
    // copy and paste it in request body params

    {
    "keyType": "PerVasp",
    "beneficiaryVaspId": "16384656509591635927", // your VASP ID
    "assetInfo": {
      "symbol": "ETH",
      "amount": "231.0",
      "isExceedingThreshold": true,
      "tradeCurrency": "KRW",
      "tradePrice": "87681287",
      "tradeISODatetime": "2022-02-08T13:02:57.824Z"
    },
    "requiredBeneficiaryInfo": "NATURAL_PERSON_NAME,ACCOUNT_NUMBER",
    "payload": {
      "version": "1.0",
      "ivms101": {
        "originator": {
          "originatorPersons": [
            {
              "naturalPerson": {
                "name": {
                  "nameIdentifier": [
                    {
                      "primaryIdentifier": "Robbins",
                      "secondaryIdentifier": "Taylor",
                      "nameIdentifierType": "LEGL"
                    }
                  ]
                },
                "dateAndPlaceOfBirth": {
                  "dateOfBirth": "1991-05-03",
                  "placeOfBirth": "Seoul"
                }
              }
            }
          ],
          "accountNumber": ["0x5811001506550d8356a215be229c15b6ef371a9a"]
        },
        "beneficiary": {
          "beneficiaryPersons": [
            {
              "naturalPerson": {
                "name": {
                  "nameIdentifier": [
                    {
                      "primaryIdentifier": "last name",
                      "secondaryIdentifier": "first name",
                      "nameIdentifierType": "LEGL"
                    }
                  ]
                }
              }
            }
          ],
          "accountNumber": ["0xb0bFf9721871e22653358956cf59a5FdBF3D752F"]
        }
      }
    }
    }
    ```
  </Accordion>
* **기대 결과**
  * 귀사의 VASP가 진행한 검증 결과에 따라 아래의 값 중의 하나를 반환합니다.
    * `VERIFIED`
    * `UNKNOWN-SYMBOL`
    * `UNKNOWN-ADDRESS`
    * `UNVERIFIED-KYC`
    * `MISMATCHED-NAME`
    * `UNAVAILABLE-INFORMATION`
    * `LACK-OF-INFORMATION`
    * `BLACKLISTED`

<br />

### 3-1. 온체인 전송 트랜잭션 실행 Test

* **조건**

  * Robot VASP가 실행한 User Verification의 결과가 `VERIFIED`여야 진행할 수 있습니다.
  * Robot VASP에게 가상 자산을 전송받을 수신인 주소는 출금 시나리오 테스트에서 가상 자산을 Robot VASP에게 전송한 송신인 주소와 일치해야 합니다.
  * 입금 시나리오 테스트에서 전송할 가상 자산의 총량은 출금 시나리오 테스트에서 Robot VASP에게 전송한 가상 자산의 총량을 초과할 수 없습니다.
  * Robot VASP Withdrawal Request API를 호출해 Robot VASP가 가상 자산 전송 트랜잭션을 실행하도록 명령해야 합니다.

  <br />

  <Accordion title="Robot VASP Withdrawal Request API 호출 방법">
    **Method**: `POST`

    * **Endpoint**: `https://api.verifyvasp.xyz/vega/robot/v1.0/action/withdrawal`

    * **요청 쿼리 파라미터**

    | Parameter Name     | Type    | Description                                                                                              |
    | ------------------ | ------- | -------------------------------------------------------------------------------------------------------- |
    | `verificationUuid` | string  | UUser Verification을 특정하는 고유 식별자. Encalve API의 User Verification API를 호출해 확인할 수 있습니다.                     |
    | `omitTxReport`     | boolean | 출금 트랜잭션을 실행한 후 트랜잭션 결과 보고 수행 여부를 판단하는 값. `true`로 설정한 경우, 트랜잭션 실행 후 트랜잭션 결과를 보고하지 않습니다. 기본값은 `false` 입니다. |

    * **요청 Body 에제**

    ```
    {
    "verificationUuid": "ecb457e3-2307-4e72-8a42-16a3774e154b", 
    "omitTxReport": false 
    }
    ```
  </Accordion>
* **기대 결과**
  * 귀사의 VASP가 입금을 확인할 수 있습니다.

**Case 1. 트랜잭션 실행 후 트랜잭션 결과 전송**

* **조건**
  * Robot VASP가 가상 자산 전송 트랜잭션을 실행한 후 귀사의 VASP가 구현한 VASP API 중 Callback API 실행해 트랜잭션 결과를 전송합니다.
* **기대 결과**
  * 귀사의 VASP가 Callback API를 통해 트랜잭션 결과를 확인할 수 있습니다.

<br />

**Case 2. 트랜잭션 실행 후 트랜잭션 결과 미전송**

* **조건**

  * Robot VASP Withdrawal Request API를 호출할 때, `omixTxReport` 필드를 `true`로 설정하여 Robot VASP가 트랜잭션 실행 결과를 전송하지 않습니다.
  * 귀사의 VASP가 Enclave API 중 Check Transaction Status API를 호출하여 트랜잭션의 현재 상태를 조회합니다.
  * 가상 자산 전송 트랜잭션 없이 Transaction Report API를 테스트 해보고 싶은 VASP를 위해 Transaction Reporting Simulation API를 지원합니다.

  <br />

  <Accordion title="Transaction Reporting Simulation API 호출 방법">
    **Method**: `POST`

    * **Endpoint**: `https://api.verifyvasp.xyz/vega/robot/v1.0/action/verifications/tx`

    * **요청 바디 파라미터**

    | Parameter Name     | Type   | Description                                                                         | Example                                        |
    | ------------------ | ------ | ----------------------------------------------------------------------------------- | ---------------------------------------------- |
    | `verificationUuid` | string | User Verification을 특정하는 고유 식별자. Encalve API의 User Verification API를 호출해 확인할 수 있습니다. | `"ecb457e3-2307-4e72-8a42-16a3774e154b"`       |
    | `txHash`           | string | 트랜잭션을 구분하는 고유 식별자.                                                                  | `"0xaaa042c0632f4d44c7cea978f22cd02e751a410e"` |

    * **요청 Body 예제**

    ```
    {
    "verificationUuid": "f02081b4-1837-41c0-a96c-221399db46d2",
    "txhash": "0xaaa042c0632f4d44c7cea978f22cd02e751a410e"
    }
    ```
  </Accordion>
* **기대 결과**
  * 귀사의 VASP가 Enclave API 중 Check Transaction Status API를 호출해 트랜잭션의 현재 상태를 조회할 수 있습니다.

<br />

### 3-2. 검증 완료 후 트랜잭션 취소 Test

* **조건**

  * Robot VASP가 가상 자산 전송 트랜잭션을 실패하지 않는 이상 VV Central 서버에 에러를 보고하지 않습니다.
  * 테스트를 진행하기 위해, Error Situation Reporting Simulation API를 호출해 Robot VASP가 에러를 보고하도록 명령해야 합니다.

  <br />

  <Accordion title="Error Situation Reporting Simulation API 호출 방법">
    **Method**: `POST`

    * **Endpoint**: `https://api.verifyvasp.xyz/vega/robot/v1.0/action/verifications/error`

    * **요청 쿼리 파라미터**

    | Field Name         | Type   | Description                                                                         | Example                                  |
    | ------------------ | ------ | ----------------------------------------------------------------------------------- | ---------------------------------------- |
    | `verificationUuid` | string | User Verification을 특정하는 고유 식별자. Encalve API의 User Verification API를 호출해 확인할 수 있습니다. | `"ecb457e3-2307-4e72-8a42-16a3774e154b"` |
    | `result`           | string | User Verification의 결과.                                                              | `"DENIED"`                               |
    | `reason`           | string | `result`가 DENIED일 때 사유를 설명하는 필드. `DENIED`일 때에만 확인할 수 있는 필드 입니다.                     | `"USER-CANCELED"`                        |
    | `message`          | string | `result`가 DENIED일 때 자세한 이유를 설명하는 필드. `DENIED`일 때에만 확인할 수 있는 필드 입니다.                 | `"User canceled this transaction"`       |

    * **요청 Body 예제**

    ```
    {
    "verificationUuid": "f02081b4-1837-41c0-a96c-221399db46d2", // previous successful verification
    "result": "DENIED",
    "reason": "USER-CANCELED",
    "message": "User canceled this transaction"
    }
    ```
  </Accordion>
* **기대 결과**
  * 귀사의 VASP가 Enclave API 중 Check Transaction Status API를 호출해 트랜잭션의 현재 상태를 확인할 수 있습니다.