---
title: 입금 시나리오 테스트
excerpt: 본 문서 가이드를 통해 입금 시나리오를 확인하고 테스트 케이스와 기대 동작을 확인할 수 있습니다.
deprecated: false
hidden: true
metadata:
  robots: index
---
> 💡 Please Note:
>
> 1. 입금 테스트는 **Ethereum Sepolia** 및 **Ripple Testnet**에서만 수행 가능합니다. 테스트를 시작하기에 앞서, 해당 환경에서 자산 전송 테스트가 가능한지 여부와 VASP 입금 계좌 등 필요한 정보를 미리 확인하십시오.
> 2. 입금 테스트를 진행하기 위해 출금 테스트가 선행되어야 합니다. 출금 테스트를 진행하지 않은 사용자는 [출금 테스트]()를 먼저 진행한 후 입금 테스트를 진행해 주십시오.
> 3. 입금 테스트에서 당신의 VASP로 입금될 자산의 총량은 출금 테스트에서 Robot VASP에게 전송한 디지털 자산의 총량을 초과할 수 없습니다.

## Deposit Test Scenario

1. **Verify the Beneficiary's Account Provided by Robot VASP.**

2. **Verify the Beneficiary's Personal Information Provided by Robot VASP.**

3-1. **Execute the transaction on the blockchain network.**

3-2. **Cancel the transaction that has completed user verification.**

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

<br />

<br />

## Test User Informations of RobotVASP

This test provides you to perform testing for the following three cases.

1. **Virtual asset transfer requested by an individual user who has completed KYC verification**
2. **Virtual asset transfer requested by an individual user who has not completed KYC verification**
3. **Virtual asset transfer requested by a corporate user who has completed KYC verification**

You can find detailed user information for each case by expanding the toggle below. Based on this information, you can interact with Robot VASP to conduct the tests.

<Accordion title="User 1(Individual user who has completed KYC verification)">
  If all the information is entered correctly, the verification result must return VERIFIED.

  ### Individual Information

  * **Last Name**: Robbins

  * **First Name**: Taylor

  ### Wallet Address

  **'ETH' address**: `0xFa230E9cCAF5e382539147294d7965Eeccbbfa5c`

  **'XRP' address**: `rGFFufDwabHuPur9927p1EgBTcCBfsjtEU`

  **'XRP' destination tag**: `123456789`
</Accordion>

<Accordion title="User 2(Individual user who has not completed KYC verification)">
  Even if all information is entered correctly, the verification result must return `DENIED`.

  ### Individual Information

  * **Last Name**: Cook

  * **First Name**: Ethan

  ### Wallet Address

  **'ETH' address**: `0x319E92715729c46869ed31d228f3b4f31e951450`

  **'XRP' address**: `rGFFufDwabHuPur9927p1EgBTcCBfsjtEU`

  **'XRP' destination tag**: `345678912`
</Accordion>

<Accordion title="User 3(Corporate user who has completed KYC verification)">
  If all the information is entered correctly, including the representative's name, the verification result must return `VERIFIED`.
  If the representative's name is missing or incorrect, the verification result should return `DENIED`, even if all other information is entered correctly.

  ### Individual Information

  * **Corporate Name**: Garrit Studio

    ### Representative Name

  * **Last Name**: Clarke

  * **First Name**: Jason

  ### Wallet Address

  **'ETH' address**: `0x26704Dc20d0ddF6cAa45b4D2b8AcB643015B951E`

  **'XRP' address**: `rGFFufDwabHuPur9927p1EgBTcCBfsjtEU`

  **'XRP' destination tag**: `234567891`
</Accordion>

<br />

## Test Cases

Your VASP must pass all test cases listed below.

### 1. Your VASP verifies the Beneficiary's account provided by Robot VASP

* **Conditions**

  * Your VASP must use the Robot VASP API to request that Robot VASP initiate the User Account Verification API.

  <br />

  <Accordion title="How to use the User Account Verification Simulation API">
    **Method**: `POST`

    * **Endpoint**: `https://api.verifyvasp.xyz/vega/robot/v1.0/action/verifications/account`

    * **Request Body Example**

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
* **Expected Result**
  * As the information written in the Request of the Robot VASP API, the VASP API implemented by your VASP must return the result listed below.
    * `VERIFIED`
    * `UNKNOWN-SYMBOL`
    * `UNKNOWN-ADDRESS`
    * `UNVERIFIED-KYC`
    * `UNAVAILABLE-INFORMATION`
    * `LACK-OF-INFORMATION`
    * `BLACKLISTED`

<br />

### 2. Your VASP verifies the Beneficiary's personal information provided by Robot VASP

* **Conditions**

  * Your VASP must use the Robot VASP API to request that Robot VASP initiate the User Verification API.
  * The Robot VASP must return `VERIFIED` as the result of User Account  Verification Simulation API before using User Verification Simulation API.

  <br />

  <Accordion title="How to use the User Verification Simulation API">
    **Method**: `POST`

    * **Endpoint**: `https://api.verifyvasp.xyz/vega/robot/v1.0/action/verifications`

    * **Request Body Example**

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
* **Expected Result**
  * You can receive seven possible responses depending on how your VASP entered the test user information.
    * `VERIFIED`
    * `UNKNOWN-SYMBOL`
    * `UNKNOWN-ADDRESS`
    * `UNVERIFIED-KYC`
    * `MISMATCHED-NAME`
    * `UNAVAILABLE-INFORMATION`
    * `LACK-OF-INFORMATION`
    * `BLACKLISTED`

<br />

### 3-1. Execute the transaction on the blockchain network

* **Conditions**

  * Only completed verifications can be used for the deposit test.
  * In this test, the originator wallet address must same with the beneficiary wallet address in the withdrawal test.
  * In this test, you cannot exceed the amount of virtual asset that your VASP has transferred in the withdrawal test for the deposit.
  * Your VASP must use the Robot VASP API to request that Robot VASP initiate the virtual asset transfer transaction.

  <br />

  <Accordion title="How to use the Robot VASP Withdrawal Request API">
    **Method**: `POST`

    * **Endpoint**: `https://api.verifyvasp.xyz/vega/robot/v1.0/action/withdrawal`

    * **Request Query**

    | Parameter Name     | Type    | Description                                                                                                                                                                                                                                          |
    | ------------------ | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
    | `verificationUuid` | string  | Identifier to distinguish User Verification. You can receive this identifier as a response after calling the User Verification API (Enclave API).                                                                                                    |
    | `omitTxReport`     | boolean | Used to verify whether the transaction report is submitted after withdrawal. If this field is set to `true`, the transaction result will not be submitted. Your VASP can set this field to `true` to conduct test case 2 in 3-1. Default is `false`. |

    * **Request Body Example**

    ```
    {
    "verificationUuid": "ecb457e3-2307-4e72-8a42-16a3774e154b", 
    "omitTxReport": false 
    }
    ```
  </Accordion>
* **Expected Result**
  * Your VASP can confirm the deposit.

**Case 1. Send the Transaction ID (Transaction Hash) to the VV Central Server after executing the transaction**

* **Conditions**
  * After the actual transaction is executed, the Robot VASP calls the Callback API (VASP API) implemented by your VASP to report the transaction within a few seconds.
* **Expected Results**
  * Your VASP can receive the transaction report through the callback API(VASP API).

<br />

<br />

**Case 2. Do not send the Transaction ID(Transaction Hash) to the VV Central Server after executing the transaction.**

* **Conditions**

  * If you set the omitTxReport field to true when calling the Robot VASP's Withdrawal Request API, Robot VASP will not perform a transaction report after executing the transaction.
  * Your VASP must check the transaction status using the Check Transaction Status API(Enclave API).
  * For the VASP who want to conduct the transaction report API test without virtual asset transfer, the Robot VASP provides Transaction Reporting Simulation API.

  <br />

  <Accordion title="How to use the Transaction Reporting Simulation API">
    **Method**: `POST`

    * **Endpoint**: `https://api.verifyvasp.xyz/vega/robot/v1.0/action/verifications/tx`

    * **Request Query**

    | Parameter Name     | Type   | Description                                                                                                                                       | Example                                        |
    | ------------------ | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------- |
    | `verificationUuid` | string | Identifier to distinguish User Verification. You can receive this identifier as a response after calling the User Verification API (Enclave API). | `"ecb457e3-2307-4e72-8a42-16a3774e154b"`       |
    | `txHash`           | string | Identifier to distinguish transactions                                                                                                            | `"0xaaa042c0632f4d44c7cea978f22cd02e751a410e"` |

    * **Request Body Example**

    ```
    {
    "verificationUuid": "f02081b4-1837-41c0-a96c-221399db46d2",
    "thrash": "0xaaa042c0632f4d44c7cea978f22cd02e751a410e"
    }
    ```
  </Accordion>
* **Expected Results**
  * Your VASP can check the transaction status through the Check Transaction Status API(Enclave API).

<br />

### 3-2. Cancel the transaction that has completed user verification

* **Conditions**

  * Robot VASP does not send an Error Report to the VV Central Server unless the virtual asset transfer fails.
  * To test an error reporting scenario, you must call the Robot VASP Error Situation Reporting Simulation API to request an Error Report.

  <br />

  <Accordion title="How to use the Error Situation Reporting Simulation API">
    **Method**: `POST`

    * **Endpoint**: `https://api.verifyvasp.xyz/vega/robot/v1.0/action/verifications/error`

    * **Request Query**

    | Field Name         | Type   | Description                                                                                                                                       | Example                                  |
    | ------------------ | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------- |
    | `verificationUuid` | string | Identifier to distinguish User Verification. You can receive this identifier as a response after calling the User Verification API (Enclave API). | `"ecb457e3-2307-4e72-8a42-16a3774e154b"` |
    | `result`           | string | The result of verification                                                                                                                        | `"DENIED"`                               |
    | `reason`           | string | The reason code for the verification result being `DENIED`. This field is only shown when the value of the `result` field is `DENIED`.            | `"USER-CANCELED"`                        |
    | `message`          | string | More details about the verification result being `DENIED`. This field is only shown when the value of the `result` field is `DENIED`.             | `"User canceled this transaction"`       |

    * **Request Body Example**

    ```
    {
    "verificationUuid": "f02081b4-1837-41c0-a96c-221399db46d2", // previous successful verification
    "result": "DENIED",
    "reason": "USER-CANCELED",
    "message": "User canceled this transaction"
    }
    ```
  </Accordion>
* **Expected Results**
  * Your VASP can check the transaction status through the Check Transaction Status API(Enclave API).