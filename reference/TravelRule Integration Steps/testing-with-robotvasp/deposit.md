---
title: Deposit
excerpt: >-
  This guide provides the withdrawal test scenario, test cases and expected
  results for the Deposit test.
deprecated: false
hidden: true
metadata:
  robots: index
---
> 💡 Please Note:
>
> 1. The deposit test is supported only in the Ethereum Sepolia and Ripple Testnet environments.\
>    Please double-check the environment before executing the virtual asset transfer transaction.
> 2. To conduct the Deposit test, your VASP must perform the withdrawal test and deposit the virtual asset during it.
> 3. The deposit amount cannot exceed the total amount of virtual assets transferred by your VASP to Robot VASP during the withdrawal test.

## Deposit Test Scenario

1. **Verify the Beneficiary's Account Provided by Robot VASP.**

2. **Verify the Beneficiary's Personal Information Provided by Robot VASP.**

3-1. **Execute the transaction on the blockchain network.**

* Case 1: Send the Transaction ID (Transaction Hash) to the VV Central Server after executing the transaction.
* Case 2: Do not send the Transaction ID(Transaction Hash) to the VV Central Server after executing the transaction.

3-2. **Cancel the transaction that has completed user verification.**

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