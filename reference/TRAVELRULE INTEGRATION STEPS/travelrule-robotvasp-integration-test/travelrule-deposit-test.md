---
title: Deposit Scenario Test
excerpt: >-
  This guide provides the deposit scenario, including the test cases and
  expected behavior.
deprecated: false
hidden: false
metadata:
  robots: index
---
<Callout icon="💡" theme="default">
  ### Please Note:

  1. Deposit tests can only be performed on **Ethereum Sepolia** and **Ripple Testnet**.
  2. Before starting, ensure that asset transfers can be tested in the given environment and that you have the necessary information such as the VASP’s deposit account.
  3. A withdrawal test must be completed before the deposit test. If you have not run the [Withdrawal Test](ref:travelrule-withdrawal-test) , please complete it first before proceeding with the deposit test.
  4. The total amount of assets deposited to your VASP in the deposit test cannot exceed the total amount of virtual assets sent to the Robot VASP during the withdrawal test.
</Callout>

## Test Flow

The deposit scenario test proceeds in the following order:

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
      <div class="vasp-step-title">User Account Verification: Instruct the Robot VASP to send a verification request for the test beneficiary account information, and confirm whether the expected response is received.</div>
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">2</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">User Verification: Instruct the Robot VASP to send a verification request for the test beneficiary information, and confirm whether the expected response is received.</div>
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">3-1</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">Proceed with the scenario where the Robot VASP executes the asset transfer transaction after verification. Test the following two sub-cases:</div>
      (Case 1)Transaction report sent (Case 2)Transaction report not sent
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">3-2</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">Proceed with the scenario where the Robot VASP cancels the transaction after verification. Test the following two sub-cases:</div>
      (Case 1)Error report sent (Case 2)Error report not sent
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">4</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">Proceed with the scenario to check the transaction status.</div>
    </div>
  </li>
</ol>
`}</HTMLBlock>

<br />

## Test Data

The virtual user information used for testing is as follows. This information is pre-stored in the Robot VASP (acting as the beneficiary VASP) and can be verified.

<Accordion title="User 1(Individual user with completed KYC)">
  If the information is entered correctly, the Robot VASP returns VERIFIED.

  ### Personal information

  * **last name**: Robbins

  * **first name**: Taylor

  ### wallet address

  **'ETH' address**: `0xFa230E9cCAF5e382539147294d7965Eeccbbfa5c`

  **'XRP' address**: `rGFFufDwabHuPur9927p1EgBTcCBfsjtEU`

  **'XRP' destination tag**: `123456789`
</Accordion>

<Accordion title="User 2(Individual user without completed KYC)">
  Even if the information is entered correctly, the Robot VASP returns DENIED.

  ### Personal information

  * **last name**: Cook

  * **first name**: Ethan

  ### wallet address

  **'ETH' address**: `0x319E92715729c46869ed31d228f3b4f31e951450`

  **'XRP' address**: `rGFFufDwabHuPur9927p1EgBTcCBfsjtEU`

  **'XRP' destination tag**: `345678912`
</Accordion>

<Accordion title="User 3(Corporate user with completed KYC)">
  If all information, including the representative’s name, is entered correctly, the Robot VASP returns VERIFIED. However, if the representative’s name is incorrect, even when all other information is correct, the Robot VASP returns DENIED.

  ### Personal information

  * **entity name**: Garrit Studio

    ### Representative name

  * **last name**: Clarke

  * **first name**: Jason

  ### wallet address

  **'ETH' address**: `0x26704Dc20d0ddF6cAa45b4D2b8AcB643015B951E`

  **'XRP' address**: `rGFFufDwabHuPur9927p1EgBTcCBfsjtEU`

  **'XRP' destination tag**: `234567891`
</Accordion>

<br />

## Test Cases

### 1. User Account Verification Test

* **Condition**

  * The Robot VASP must be instructed to send a User Account Verification request to your VASP.
  * You can call the User Account Verification Simulation API to instruct the Robot VASP to send the request.

  <br />

  <Accordion title="User Account Verification Simulation API Invocation">
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
  * Based on the test data, the verification should return one of the following results, including the expected reason codes for all possible `DENIED` cases:
    * `VERIFIED`
    * `UNKNOWN-SYMBOL`
    * `UNKNOWN-ADDRESS`
    * `UNVERIFIED-KYC`
    * `MISMATCHED-NAME`

<br />

### 2. User Verification Test

* **Condition**

  * The Robot VASP must be instructed to send a User Verification request to your VASP.
  * You can call the User Verification Simulation API to instruct the Robot VASP to send the request.
  * The Originator’s Account Number (sending address) used by the Robot VASP must match the beneficiary address that received virtual assets during the withdrawal scenario test.
  * The total amount of virtual assets sent in the deposit test must not exceed the total amount sent to the Robot VASP in the withdrawal test.
  * Before running the User Verification Simulation API, you must first call the User Account Verification Simulation API and receive a `VERIFIED` response.

  <br />

  <Accordion title="User Verification Simulation API Invocation">
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
  * Depending on your VASP’s verification result, one of the following values is returned:
    * `VERIFIED`
    * `UNKNOWN-SYMBOL`
    * `UNKNOWN-ADDRESS`
    * `UNVERIFIED-KYC`
    * `MISMATCHED-NAME`
    * `UNAVAILABLE-INFORMATION`
    * `LACK-OF-INFORMATION`
    * `BLACKLISTED`

<br />

### 3-1. On-Chain Transaction Execution Test

* **Condition**

  * The result of the User Verification executed by the Robot VASP must be `VERIFIED`.
  * Call the Robot VASP Withdrawal Request API to instruct the Robot VASP to execute the virtual asset transfer transaction.

  <br />

  <Accordion title="Robot VASP Withdrawal Request API Invocation">
    **Method**: `POST`

    * **Endpoint**: `https://api.verifyvasp.xyz/vega/robot/v1.0/action/withdrawal`

    * **Request Parameters**

    | Parameter Name     | Type    | Description                                                                                                                                         |
    | ------------------ | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
    | `verificationUuid` | string  | Unique identifier specifying the User Verification. You can verify it by calling the User Verification API from the Enclave API.                    |
    | `omitTxReport`     | boolean | Determines whether to send a transaction result report after executing a withdrawal transaction. If set to true, no report is sent. Default: false. |

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

**Case 1. Transaction Executed and Result Reported (omitTxReport = false)**

* **Condition**
  * After executing the virtual asset transfer transaction, the Robot VASP sends the Transaction Result Report to your VASP.
* **Expected Result**
  * Your VASP can confirm the transaction result via the Callback API.

<br />

**Case 2. Transaction Executed and Result Not Reported (omitTxReport = true)**

* **Condition**

  * When calling the Robot VASP Withdrawal Request API, set the `omitTxReport` field to `true` to prevent the Robot VASP from sending the transaction execution result.
  * Your VASP must call the Check Transaction Status API from the Enclave API to check the current transaction status.
  * If you want the Robot VASP to send a Transaction Report without actually transferring virtual assets, call the Transaction Reporting Simulation API.

  <br />

  <Accordion title="Transaction Reporting Simulation API Invocation">
    **Method**: `POST`

    * **Endpoint**: `https://api.verifyvasp.xyz/vega/robot/v1.0/action/verifications/tx`

    * **Request Parameters**

    | Parameter Name     | Type   | Description                                                                                                                      | Example                                        |
    | ------------------ | ------ | -------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------- |
    | `verificationUuid` | string | Unique identifier specifying the User Verification. You can verify it by calling the User Verification API from the Enclave API. | `"ecb457e3-2307-4e72-8a42-16a3774e154b"`       |
    | `txHash`           | string | Unique identifier specifying the transaction hash.                                                                               | `"0xaaa042c0632f4d44c7cea978f22cd02e751a410e"` |

    * **Request Body Example**

    ```
    {
      "verificationUuid": "f02081b4-1837-41c0-a96c-221399db46d2",
      "txhash": "0xaaa042c0632f4d44c7cea978f22cd02e751a410e"
    }
    ```
  </Accordion>
* **Expected Result**
  * Your VASP can check the current transaction status by calling the Check Transaction Status API.

<br />

### 3-2. Cancel Transaction After Verification Test

* **Condition**

  * Unless the Robot VASP encounters a failed virtual asset transfer transaction, it does not report an error to the VV Central server.
  * To run this test, call the Error Situation Reporting Simulation API to instruct the Robot VASP to report an error.

  <br />

  <Accordion title="Error Situation Reporting Simulation API Invocation">
    **Method**: `POST`

    * **Endpoint**: `https://api.verifyvasp.xyz/vega/robot/v1.0/action/verifications/error`

    * **Request Parameters**

    | Field Name         | Type   | Description                                                                                                                      | Example                                  |
    | ------------------ | ------ | -------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------- |
    | `verificationUuid` | string | Unique identifier specifying the User Verification. You can verify it by calling the User Verification API from the Enclave API. | `"ecb457e3-2307-4e72-8a42-16a3774e154b"` |
    | `result`           | string | Result of the User Verification.                                                                                                 | `"DENIED"`                               |
    | `reason`           | string | Specifies the reason when result is DENIED. This field is only applicable when the value is DENIED.                              | `"USER-CANCELED"`                        |
    | `message`          | string | Describes the reason in detail when result is DENIED. This field is only applicable when the value is DENIED.                    | `"User canceled this transaction"`       |

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
* **Expected Result**
  * Your VASP can check the current transaction status by calling the Check Transaction Status API from the Enclave API.