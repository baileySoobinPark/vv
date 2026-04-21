---
title: Deposit Scenario Test
excerpt: This guide explains the deposit scenario, test cases, and expected results.
deprecated: false
hidden: false
metadata:
  robots: index
---
<Callout icon="💡" theme="default">
  ### Test Environment Limitations

  Deposit tests can only be performed on the following networks:

  * Ethereum Sepolia
  * Ethereum Holesky
  * Ripple Testnet

  Before starting, confirm that asset transfer testing is available in your chosen environment and ensure you have the required VASP withdrawal wallet addresses and related information.
</Callout>

<br />

# Overview

Deposit scenario testing covers two cases:

* **Pre-Verification Deposit Test** – Assumes receiving from a VASP with regulatory obligations.
* **Post-Verification Deposit Test** – Assumes receiving from a VASP without regulatory obligations.

Run each scenario to verify your VASP’s implementation of the VerifyName protocol.

<br />

## Test Data

Use the provided virtual user data for testing. This information is pre-registered with the RobotVASP (acting as the beneficiary VASP) and can be successfully verified.

<Accordion title="RobotVASP KR" icon="fa-info-circle">
  Robot VASP in the KR region.

  ### Information

  * **url**: [https://api-kr.verifyvasp.xyz/vega/robot](https://api-kr.verifyvasp.xyz/vega/robot)

  * **name**: RobotVASP KR

  * **vaspId**: 4855741808338010592

  * **testnet**: Ethereum Sepolia, Ripple
</Accordion>

<Accordion title="RobotVASP Global" icon="fa-info-circle">
  Robot VASP in the Global region.

  ### Information

  * **url**: [https://api.verifyvasp.xyz/vega/robot](https://api.verifyvasp.xyz/vega/robot)

  * **name**: RobotVASP

  * **vaspId**: 17104899575711300402

  * **testnet**: Ethereum Holesky, Ripple
</Accordion>

<Accordion title="User 1(KYC-Verified Individual User)" icon="fa-info-circle">
  When all information below is entered correctly, the Robot VASP will return 'VERIFIED'.

  ### Personal information

  * **last name**: Robbins

  * **first name**: Taylor

  * **date of birth**: 1981-01-01

  ### wallet address

  **'ETH' address**: `0xFa230E9cCAF5e382539147294d7965Eeccbbfa5c`

  **'XRP' address**: `rGFFufDwabHuPur9927p1EgBTcCBfsjtEU`

  **'XRP' destination tag**: `123456789`
</Accordion>

<Accordion title="User 2(Non-KYC Individual User)" icon="fa-info-circle">
  Even if all information below is entered correctly, the Robot VASP will return 'DENIED'.

  ### Personal information

  * **last name**: Cook

  * **first name**: Ethan

  * **date of birth**: 1991-10-23

  ### wallet address

  **'ETH' address**: `0x319E92715729c46869ed31d228f3b4f31e951450`

  **'XRP' address**: `rGFFufDwabHuPur9927p1EgBTcCBfsjtEU`

  **'XRP' destination tag**: `345678912`
</Accordion>

<Accordion title="User 3(KYC-Verified Corporate User)" icon="fa-info-circle">
  When all information, including the representative’s name, is entered correctly, the Robot VASP will return 'VERIFIED'. If all information is correct except for the representative’s name, the Robot VASP will return 'DENIED'.

  ### Entity information

  * **Entity name**: Garrit Studio

  * **date of incorporation**: 2019-04-01

  * **LEI**: 506700GE1G29325QX363

  * **Business Idnetifier Code(BIC)**: BNKYUS33

  * **Idnetification**: 5493001KJTIIGC8Y1R12

  * **Issuer**: ISO17442

  ### Representative name

  * **last name**: Clarke

  * **first name**: Jason

  ### wallet address

  **'ETH' address**: `0x26704Dc20d0ddF6cAa45b4D2b8AcB643015B951E`

  **'XRP' address**: `rGFFufDwabHuPur9927p1EgBTcCBfsjtEU`

  **'XRP' destination tag**: `234567891`
</Accordion>

<br />

# Pre-Verification Deposit Test Flow

This scenario assumes that RobotVASP (with regulatory obligations) performs recipient verification before transferring virtual assets to your VASP.

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
      <div class="vasp-step-title">Owner Verification – RobotVASP sends a verification request to your VASP.</div>
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">2</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">Result Report – RobotVASP reports the final verification result.</div>
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">3</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">Execute the on-chain asset transfer transaction.</div>
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">4</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">Confirm transfer completion.</div>
    </div>
  </li>
</ol>
`}</HTMLBlock>

<br />

## Test Cases

### 1. Owner Verification Test

* **Conditions**

  * Call the Owner Verification Simulation API to instruct RobotVASP to send a verification request.
  * RobotVASP requests beneficiary verification from your VASP.
  * Set verification type to `VerifyBeneficiary`.

  <Accordion title="Owner Verification Simulation API Call Exmple" icon="fa-info-circle">
    **Method**: `POST`

    * **Endpoint**: `<RobotVASP URL>/vega/robot/v2.0/action/owner-verifications`

      * Example: Verifying an Individual Account

        ```json
        {
          "supplementary_data": {
            "envelope": {
              "key_type": "PerVerification",
              "vasp_id": "<Your VASP ID>",
              "type": "VerifyBeneficiary",
              "ticker": "ETH",
              "network": "ethereum",
              "address": "<Beneficiary Address>"
            }
          },
          "creditor": {
            "name": "Taylor Robbins",
            "supplementary_data": {
              "envelope": {
                "name": {
                  "first_name": "Taylor",
                  "last_name": "Robbins"
                }
              }
            },
            "identification": {
              "private_identification": {
                "date_and_place_of_birth": {
                  "birth_date": "1981-01-01"
                }
              }
            }
          }
        }  
        ```

      * Example: Verifying a Corporate Account

        ```json
        {
          "supplementary_data": {
            "envelope": {
              "key_type": "PerVerification",
              "vasp_id": "<Your VASP ID>",
              "type": "VerifyBeneficiary",
              "ticker": "ETH",
              "network": "ethereum",
              "address": "<Beneficiary Address>"
            }
          },
          "creditor": {
            "name": "Garrit Studio",
            "identification": {
              "organisation_identification": {
                "supplementary_data": {
                  "envelope": {
                    "date_of_incorporation": "2019-04-01"
                  }
                }
              },
              "lei": "506700GE1G29325QX363",
              "bic": "BNKYUS33",
              "other": {
                "identification": "5493001KJTIIGC8Y1R12",
                "issuer": "ISO17442"
              }
            }
          }
        }      
        ```

      * Additional examples can be found in the Owner Verification API section of the Enclave API documentation.
  </Accordion>
* **Expected Result**
  * Your VASP must return a verification result for each item: `MATCHED`, `MISMATCHED`, or `SKIPPED`.

```json
{
    "request_id": "5be3c01e-2f3f-4536-a662-6c58f0465c57",
    "verification_results": {
        "ticker": "MATCHED",
        "network": "SKIPPED",
        "address": "MATCHED",
        "tx_hash": "MATCHED",
        "dti": "SKIPPED",
        "name": "MATCHED",
        "birth_date": "MATCHED",
        "date_of_incorporation": "SKIPPED",
        "organisation_identification": "SKIPPED"
    },
    "verified_at": "2025-04-21T04:40:28.536Z"
}
```

<br />

### 2. Report Verification Result Test

* **Conditions**
  * Call the Result Report Simulation API to instruct RobotVASP to report the final verification result.

    <Accordion title="Resuit Report Simulation API Call Example" icon="fa-info-circle">
      **Method**: `POST`

      * **Endpoint**: `<RobotVASP URL>/vega/robot/v2.0/action/owner-verifications/{request_id}/result`

        * **Request body example**

          ```json
          {
            "verification_result": "VERIFIED"
          }
          ```
          ```json
          {
            "verification_result": "DENIED",
            "reason": "MISMATCH-ADDRESS",
            "message": "mismatch address"
          }
          ```
          ```json
          {
            "verification_result": "ERROR",
            "reason": "TRANSFER-ERROR",
            "message": "test error"
          }
          ```

        * **Response example**
          ```json
          {
            "result": true
          }
          ```

        * Additional examples can be found in the Result Report API section of the Enclave API documentation.
    </Accordion>
* **Expected Result**
  * Your VASP’s Callback API receives the final verification result reported by RobotVASP.
  * Confirm that callbackType is OWNER_VERIFICATION_RESULT_REPORT.

<br />

### 3. On-chain Transaction Execution Test

> ❗️ Caution
>
> When testing XRP addresses, always include the destination tag.

<br />

**[Precondition 1.]** Before transfer, call the Balance Check API to confirm the current balance of the originator’s wallet.

<Accordion title="Check Balance API Call Example" icon="fa-info-circle">
  **Method**: `GET`

  * **Endpoint**: `<RobotVASP URL>/v1.0/testnet/balance`
    * **RobotVasp KR**: [https://api-kr.verifyvasp.xyz/vega/robot](https://api-kr.verifyvasp.xyz/vega/robot)
    * **RobotVasp Global**: [https://api.verifyvasp.xyz/vega/robot](https://api.verifyvasp.xyz/vega/robot)

  * **Query parameters**

    | Parameter Name | Type   | Description                                 | Example                                 |
    | -------------- | ------ | ------------------------------------------- | --------------------------------------- |
    | `vaspId`       | string | Ordering VASP ID                            | 15952089931162058999                    |
    | `symbol`       | string | Symbol                                      | ETH                                     |
    | `address`      | string | Wallet address to receive the virtual asset | 0xb0bFf9721871e2265...6cf59a5FdBF3D752F |

  * **Request example**

    ```
    https://api.verifyvasp.xyz/vega/robot/v1.0/testnet/balance?vaspId=15952089931162058999&symbol=ETH&address=0xb0bFf9721871e22653358956cf59a5FdBF3D752F
    ```

  * **Response example**
    ```json
    {
        "symbol": "ETH",
        "address": "0xFa230E9cCAF5e382539147294d7965Eeccbbfa5c",
        "vaspId": "15952089931162058999",
        "value": "0.0497"
    }
    ```
</Accordion>

* The transfer amount must not exceed the available balance returned by this API.

<br />

**[Precondition 2.]** Call the RobotVASP Withdrawal API to instruct RobotVASP to send virtual assets to your VASP-managed wallet address.

<Accordion title="RobotVASP Withdrawal API Call Example" icon="fa-info-circle">
  **Method**: `POST`

  * **Endpoint**: `<RobotVASP URL>/v2.0/action/owner-verifications/{request_id}/withdrawal`
    * **RobotVasp KR**: [https://api-kr.verifyvasp.xyz/vega/robot](https://api-kr.verifyvasp.xyz/vega/robot)
    * **RobotVasp Global**: [https://api.verifyvasp.xyz/vega/robot](https://api.verifyvasp.xyz/vega/robot)

  * **Query parameters**

    | Parameter Name              | required | Type    | Description                                                                                                      | Example                                    |
    | --------------------------- | -------- | ------- | ---------------------------------------------------------------------------------------------------------------- | ------------------------------------------ |
    | `ticker`                    | required | string  | Ticker                                                                                                           | "ETH"                                      |
    | `originator_account_number` | required | string  | Wallet address from which the virtual asset will be sent                                                         | 0xb0bFf9721871e22653358956cf59a5FdBF3D752F |
    | `originator_tag`            | optional | string  | Tag used to verify an address with a secondary address (e.g., destination tag for XRP)                           | 123456789                                  |
    | `amount`                    | optional | string  | Amount of virtual asset to be transferred                                                                        | "0.0005"                                   |
    | `omit_tx_report`            | optional | boolean | 가Field indicating whether to skip reporting the transaction ID (hash) after sending the asset. Default is false. | false                                      |

  * **Request body example**

    ```json
    {
      "ticker": "ETH",
      "originator_account_number": "0xFa230E9cCAF5e382539147294d7965Eeccbbfa5c",
      "amount": "0.001",
      "omit_tx_report": true
    }
    ```

  * **Response example**
    ```json
    {
      "tx_hash": "0x6CED59A8D2dA7bdf3E6c2920F632421A8f3c34D2"
    }
    ```
</Accordion>

<br />

* **Conditions**
  * When omit_tx_report = false, your Callback API should automatically receive the transaction hash.
* **Expected result**
  * Your Callback API receives the transaction hash (callbackType = OWNER_VERIFICATION_TX_REPORT).
    * Deposit confirmation may take up to 20 minutes

<br />

### 4. Deposit Confirmation

* **Conditions**
  * Call the Balance Check API to verify that the sender’s wallet balance has changed.
* **Expected Result**
  * Confirm that the virtual assets have been successfully transferred to the recipient address.

<br />

## Post-Verification Deposit Test Flow

This scenario assumes that RobotVASP (without regulatory obligations) sends virtual assets to your VASP (with regulatory obligations) without prior verification.

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
      <div class="vasp-step-title">RobotVASP executes the on-chain transaction to your VASP.</div>
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">2</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">Owner Verification: Your VASP sends a verification request to RobotVASP.</div>
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">3</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">Report Verification Result: Your VASP reports the final verification result.</div>
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">4</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">Confirm transfer completion.</div>
    </div>
  </li>
</ol>
`}</HTMLBlock>

<br />

## Test Cases

### 1. On-chain Transaction Execution Test

> ❗️ Caution
>
> When testing XRP addresses, always include the destination tag.

* **Conditions**

  * Check the balance of the sender’s wallet managed by RobotVASP.

    <Accordion title="Check Balance API Call Example" icon="fa-info-circle">
      **Method**: `GET`

      * **Endpoint**: `<RobotVASP URL>/v1.0/testnet/balance`
        * **RobotVasp KR**: [https://api-kr.verifyvasp.xyz/vega/robot](https://api-kr.verifyvasp.xyz/vega/robot)
        * **RobotVasp Global**: [https://api.verifyvasp.xyz/vega/robot](https://api.verifyvasp.xyz/vega/robot)

      * **Query parameters**

        | Parameter Name | Type   | Description                                 | Example                                 |
        | -------------- | ------ | ------------------------------------------- | --------------------------------------- |
        | `vaspId`       | string | Ordering VASP ID                            | 15952089931162058999                    |
        | `symbol`       | string | Symbol                                      | ETH                                     |
        | `address`      | string | Wallet address to receive the virtual asset | 0xb0bFf9721871e22653...cf59a5FdBF3D752F |

      * **Request example**

        ```
        https://api.verifyvasp.xyz/vega/robot/v1.0/testnet/balance?vaspId=15952089931162058999&symbol=ETH&address=0xb0bFf9721871e22653358956cf59a5FdBF3D752F
        ```

      * **Response example**
        ```json
        {
            "symbol": "ETH",
            "address": "0xFa230E9cCAF5e382539147294d7965Eeccbbfa5c",
            "vaspId": "15952089931162058999",
            "value": "0.0497"
        }
        ```
    </Accordion>
  * Instruct RobotVASP to send virtual assets to your VASP without pre-verification.
  * Depending on blockchain network conditions, block confirmation may take over 15 minutes.

  <br />

  <Accordion title="RobotVASP Unverified Withdrawal API Call Example" icon="fa-info-circle">
    **Method**: `POST`

    * **Endpoint**: `<RobotVASP URL>/v2.0/action/unverified-withdrawal`
      * **RobotVasp KR**: [https://api-kr.verifyvasp.xyz/vega/robot](https://api-kr.verifyvasp.xyz/vega/robot)
      * **RobotVasp Global**: [https://api.verifyvasp.xyz/vega/robot](https://api.verifyvasp.xyz/vega/robot)

    * **Query parameters**

      | Parameter Name             | required | Type   | Description                                                                                                                                          | Example                                    |
      | -------------------------- | -------- | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------ |
      | `beneficiaryVaspId`        | required | string | The VASP ID of the VASP managing the recipient address of the virtual asset transfer. In this test, it refers to your VASP ID.                       | 15952089931162058999                       |
      | `ticker`                   | required | string | Ticker of the virtual asset to be transferred                                                                                                        | "ETH"                                      |
      | `depositTxHash`            | required | string | The hash of the transaction where your VASP transferred assets to RobotVASP. Enter the hash of the transaction used in the withdrawal scenario test. | 0xb0bFf9721871e22653358956cf59a5FdBF3D752F |
      | `beneficiaryAccountNumber` | required | string | Wallet address of the beneficiary.                                                                                                                   | 123456789                                  |
      | `beneficiaryTag`           | optional | string | Secondary address that can specify the beneficiary’s wallet address. If testing with XRP, this must be entered.                                      | 123456789                                  |
      | `amount`                   | required | string | Amount of virtual asset to be transferred                                                                                                            | "0.0005"                                   |

    * **Request body example**

      ```json
      {
        "beneficiaryVaspId": "15952089931162058999", 
        "ticker": "ETH",
        "depositTxHash": "0xbdd6e9e12514507cee06e31dd4a64acb0777f2365902ee9577c656322f9f2f74", 
        "beneficiaryAccountNumber": "0x9FC3da866e7DF3a1c57adE1a97c9f00a70f010c8",
        "amount": "0.001"
      }
      ```

    * **Response example**
      ```json
      {
        "txHash": "0x4f29e93afa059935ec5dafb26ac17cc318184907aa6bcf7937f505972cde7c1a"
      }
      ```
  </Accordion>
* **Expected result**
  * Confirm that the virtual assets were received in your VASP-managed recipient wallet.
  * Call the Check Balance API to verify that the sender’s wallet balance decreased by the transferred amount.

<br />

### 2. Owner Verification Test

* **Conditions**

  * Using the transaction hash returned by the RobotVASP Unverified Withdrawal API, request post-verification from RobotVASP.
  * Set verification type to `VerifyOriginator`

  <Accordion title="Owner Verification Simulation API Call Example" icon="fa-info-circle">
    **Method**: `POST`

    * **Endpoint**: `<Robot VASP URL>/vega/robot/v2.0/action/owner-verifications`

      * **Request body example**

        ```json
        {
          "supplementary_data": {
            "envelope": {
              "key_type": "PerVerification",
              "vasp_id": "<Your VASP ID>",
              "type": "VerifyOriginator",
              "ticker": "ETH",
              "network": "ethereum",
              "tx_hash": "0xbdd6e9e12514507cee06e31dd4a64acb0777f2365902ee9577c656322f9f2f74",
              "address": "0xFa230E9cCAF5e382539147294d7965Eeccbbfa5c"
            }
          },
          "debtor": {
            "name": "Taylor Robbins",
            "supplementary_data": {
              "envelope": {
                "name": {
                  "first_name": "Taylor",
                  "last_name": "Robbins"
                }
              }
            },
            "identification": {
              "private_identification": {
                "date_and_place_of_birth": {
                  "birth_date": "1981-01-01"
                }
              }
            }
          }
        }
        ```

      * Example: Verifying a Corporate Account

        ```json
        {
          "supplementary_data": {
            "envelope": {
              "key_type": "PerVerification",
              "vasp_id": "<Your VASP ID>",
              "type": "VerifyOriginator",
              "ticker": "ETH",
              "network": "ethereum",
              "tx_hash": "0xbdd6e9e12514507cee06e31dd4a64acb0777f2365902ee9577c656322f9f2f74",
              "address": "0xFa230E9cCAF5e382539147294d7965Eeccbbfa5c"
            }
          },
          "debtor": {
            "name": "Garrit Studio",
            "identification": {
              "organisation_identification": {
                "supplementary_data": {
                  "envelope": {
                    "date_of_incorporation": "2019-04-01"
                  }
                }
              },
              "lei": "506700GE1G29325QX363",
              "bic": "BNKYUS33",
              "other": {
                "identification": "5493001KJTIIGC8Y1R12",
                "issuer": "ISO17442"
              }
            }
          }
        }
        ```

      * Additional examples can be found in the Owner Verification API section of the Enclave API documentation.
  </Accordion>

<br />

* **Expected Result**
  * RobotVASP returns a verification result for each item: `MATCHED`, `MISMATCHED`, or `SKIPPED`.
  <br />
  ```json
  {
      "request_id": "5be3c01e-2f3f-4536-a662-6c58f0465c57",
      "verification_results": {
          "ticker": "MATCHED",
          "network": "SKIPPED",
          "address": "MATCHED",
          "tx_hash": "MATCHED",
          "dti": "SKIPPED",
          "name": "MATCHED",
          "birth_date": "MATCHED",
          "date_of_incorporation": "SKIPPED",
          "organisation_identification": "SKIPPED"
      },
      "verified_at": "2025-04-21T04:40:28.536Z"
  }
  ```
  <br />

### 3. Report Verification Result Test

* **Conditions**
  * Your VASP determines the final verification result based on the response from RobotVASP and reports it.

    <Accordion title="Result Report API Call Example" icon="fa-info-circle">
      **Method**: `POST`

      * **Endpoint**: `/v2/owner-verifications/{request_id}/result`

        * **Query Parameters**

        | Parameter Name        | Type   | Description                                                                                | Example                                                                                                                                                                                                                           |
        | --------------------- | ------ | ------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
        | `verification_result` | enum   | Final verification result                                                                  | `VERIFIED`, `DENIED`, `ERROR`                                                                                                                                                                                                     |
        | `reason`              | enum   | If verification\_result is DENIED or ERROR, this field specifies the reason for the result | `MISMATCH-TICKER` , `MISMATCH-NETWORK`, `MISMATCH-ADDRESS`, `MISMATCH-NAME`, `MISMATCH-DTI`, `MISMATCH-DATE-OF-INCORPORATION`, `MISMATCH-BIRTH-DATE`, `MISMATCH-ORGANISATION-IDENTIFICATION`, `TRANSFER-ERROR`, `UNDEFINED-ERROR` |
        | `message`             | string | If verification\_result is DENIED or ERROR, this field describes the reason in detail      | transfer error                                                                                                                                                                                                                    |

        * **Request body Example**

        ```json
        {
            "verification_result": "VERIFIED"
        }
        ```
        ```json
        {
            "verification_result": "DENIED",
            "reason": "MISMATCH-ADDRESS",
            "message": "mismatch address"
        }
        ```
        ```json
        {
            "verification_result": "ERROR",
            "reason": "TRANSFER-ERROR",
            "message": "test error"
        }
        ```

        * **Response example**

        ```json
        {
          "result": true
        }
        ```

        * Additional examples can be found in the Result Report API section of the Enclave API documentation.
    </Accordion>
* **Expected Result**
  * Your VASP determines whether to reflect the deposit based on the final verification decision.
