---
title: Deposit
excerpt: >-
  This guide provides the deposit test scenario, test cases and expected results
  for the Deposit test.
deprecated: false
hidden: true
metadata:
  robots: index
---
> 💡 Please Note:
>
> The deposit test is supported only in the Ethereum Sepolia and Ripple Testnet environments.
>
> Please double-check the environment before executing the virtual asset transfer transaction.
>
> 1. To conduct the Deposit test, your VASP must perform the withdrawal test and deposit the virtual asset during it.
> 2. The deposit amount cannot exceed the total amount of virtual assets transferred by your VASP to Robot VASP during the withdrawal test.

<br />

# Test Cases

## 1. **Your VASP verifies the Beneficiary's account provided by Robot VASP**

* **Conditions**

  * Your VASP must use the Robot VASP API to request that Robot VASP initiate the Owner Verification API.

  <br />

  <Accordion title="How to use Owner Verification Simulation API">
    **Method**: `POST`

    * **Endpoint**: `https://api.verifyvasp.xyz/vega/robot/v2.0/action/owner-verifications`

    * **Request Body Example**

    ```
    {
      "supplementary_data": {
          "envelope": {
              "vasp_id": "17100967850059048960", // your VASP ID
              "type": "VerifyBeneficiary",
              "ticker": "ETH",
              "network": "ethereum",
              "address": "0xFa230E9cCAF5e382539147294d7965Eeccbbfa5c"
          }
      },
      "creditor": {
          "name": "<full name>",   // 수정 필요
          "supplementary_data": {  // optional
              "envelope": {
                  "name": {
                      "first_name": "<first name>",
                      "last_name": "<last name>"
                  }
              }
          },
          "identification": {
              "private_identification": {
                  "date_and_place_of_birth": {
                      "birth_date": "1990-04-05"  // 수정 필요
                  }
              }
          }
      }
    }
    ```

    * **Expected Result**

    * Robot VASP 가 Beneficiary VASP 으로 부터 받은 항목별 검증 결과를 바탕으로 판단한 최종 결과가 “verification\_result” 필드로 리턴 됩니다.

    * 항목별 검증 결과 예시

    ```json
    "verification_results": {
        "ticker": "MATCHED",
        "network": "MATCHED",
        "address": "MATCHED",
        "tx_hash": "SKIPPED",
        "dti": "SKIPPED",
        "name": "MATCHED",
        "birth_date": "MATCHED",
        "date_of_incorporation": "SKIPPED",
        "organisation_identification": "SKIPPED"
    },
    ```

    * Robot VASP 가 판단한 결과를 포함한 응답
      * 어떻게 결과를 해석하는가는 VASP 에 따라 다르겠지만 Robot VASP 는 “verification\_results” 항목들 중 하나라도 MISMATCHED 가 있으면 “DENIED” 로 판단하고 있습니다.

    ```
    {
      "request_id": "cf169ab3-48f4-4f91-b63a-41a734fb0c9d",
      "verified_at": "2025-04-22T10:08:31.785Z",
      "verification_result": "VERIFIED" // "VERIFIED" | "DENIED"
    }
    ```
  </Accordion>

<br />

## 2. **Execute the transaction on the blockchain network**

* **Conditions**

  * Only completed owner verifications can be used for the deposit test.
  * In this test, the originator wallet address must same with the beneficiary wallet address in the withdrawal test.
  * In this test, you cannot exceed the amount of virtual asset that your VASP has transferred in the withdrawal test for the deposit.
  * Your VASP must use the Robot VASP API to request that Robot VASP initiate the virtual asset transfer transaction.

  <br />

  <Accordion title="How to use the Robot VASP Withdrawal Request API">
    **Method**: `POST`

    * **Endpoint**: `https://api.verifyvasp.xyz/vega/robot/v2.0/action/owner-verifications/{request_id}/withdrawal`

    * **Request Query**

    | Parameter Name                         | Type    | Description                                                                                                                                                                                                                 | Example                                      |
    | -------------------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------- |
    | `ticker` (required)                    | string  | Ticker of the virtual asset to be transferred.                                                                                                                                                                              | `ETH`                                        |
    | `originator_account_number` (required) | string  | Must be an address that has a balance greater than 0 as confirmed in withdrawal test 3-1 case 1.                                                                                                                            | `0xe6998af38840836d2469ae71aa849f4f94c2b6d6` |
    | `originator_tag` (optional)            | string  | Tag used for verification when the address includes a secondary tag (e.g., destination tag, memo).                                                                                                                          | `2852039353`                                 |
    | `amount` (required)                    | string  | Must be within the available balance verified in withdrawal test 3-1 case 1.                                                                                                                                                | –                                            |
    | `omit_tx_report` (optional)            | boolean | To verify whether the transaction report is submitted after withdrawal. If set to `true`, the transaction result will not be submitted. Your VASP can set this to `true` to conduct test case 2 in 2-1. Default is `false`. | –                                            |

    * **Expected Result**

    ```
    {
    "tx_hash": "0x116f1d11b871dfcc8c551fa146f02dfedca2ec5908338ce2c648416ceede26c2"
    }
    ```
  </Accordion>

  <br />
* **Case 1. Send the Transaction ID (Transaction Hash) to the VV Central Server after executing the transaction**
  * **Conditions**
    * After the actual transaction is executed, the Robot VASP calls the Callback API (VASP API) implemented by your VASP to report the transaction within a few seconds.
  * **Expected Results**
    * Your VASP can receive the transaction report through the callback API(VASP API).
    <br />
* **Case 2. Do not send the Transaction ID(Transaction Hash) to the VV Central Server after executing the transaction.**
  * **Conditions**

    * If you set the `omitTxReport` field to `true` when calling the Robot VASP's Withdrawal Request API, Robot VASP will not perform a transaction report after executing the transaction.
    * Your VASP must check the transaction status using the Check Transaction Inquiry API(Enclave API) for owner verification.
    * For the VASP who want to conduct the transaction report API test without virtual asset transfer, the Robot VASP provides Transaction Reporting Simulation API.

    <br />

    <Accordion title="How to use the Robot VASP Withdrawal Request API">
      **Method**: `POST`

      * **Endpoint**: `https://api.verifyvasp.xyz/vega/robot/v2.0/action/owner-verifications/{request_id}/report`

      * **Request Query**

      | Parameter Name        | Type                                                               | Description                                                                                                                                                                        | Example                                                            |
      | --------------------- | ------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
      | `verification_result` | enum (`VERIFIED`, `DENIED`, `ERROR`)                               | The verification result determined by the VASP that requested owner verification using the `VerifyBeneficiary` type.                                                               | `VERIFIED`                                                         |
      | `tx_hash`             | string **(required if`verification_result` is `VERIFIED`)**        | Unique identifier of the transaction. Required if the verification result is `VERIFIED`.                                                                                           | `8a54d58ca4100112a5430818776d74898f2232770bae03046862575cb851a042` |
      | `vout`                | string (optional)                                                  | In cases where multiple transfers can be included in a single transaction (e.g., Bitcoin), this is the index value that indicates which transfer corresponds to this verification. | `0`                                                                |
      | `reason`              | enum **(required if`verification_result` is `DENIED` or `ERROR`)** | Required when the VASP reports `DENIED` or `ERROR`. See \[reason codes]\(링크 변경 필요) for details.                                                                                    | `MISMATCH-TICKER`                                                  |
      | `message`             | string (optional)                                                  | Detailed message explaining the reason when reporting `DENIED` or `ERROR` alongside `reason`.                                                                                      | `transfer error`                                                   |

      * **Request Body Example**

      ```
      {
          "verification_result": "VERIFIED",
          "tx_hash": "0xbdd6e9e12514507cee06e31dd4a64acb0777f2365902ee9577c656322f9f2f74"
      }

      or

      {
          "verification_result": "DENIED",
          "reason": "MISMATCH-ADDRESS",
          "message": "mismatch address"
      }

      or

      {
          "verification_result": "ERROR",
          "reason": "TRANSFER-ERROR",
          "message": "test error"
      }
      ```

      * **Expected Result**

        * Your VASP can check the transaction status through the Check Transaction Inquiry API(`v2/owner-verifications/{request_id}/tx/inquiry`, Enclave API).
    </Accordion>