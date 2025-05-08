---
title: Withdrawal
excerpt: >-
  This guide provides the withdrawal test scenario, test cases and expected
  results for the withdrawal test.
deprecated: false
hidden: true
metadata:
  robots: index
---
> 💡 Please Note:
>
> The withdrawal test is supported only in the Ethereum Sepolia and Ripple Testnet environments.
>
> Please double-check the environment before executing the virtual asset transfer transaction.

<br />

## Withdrawal Test Scenario

1. Identifying the Robot VASP to interact with your VASP

2. The Robot VASP verifies the Beneficiary's **account information** provided by your VASP.

3. The Robot VASP verifies the Beneficiary's **personal information** provided by your VASP.

4-1. Your VASP executes a virtual asset transfer transaction on the blockchain network.

Case 1: After executing the transaction, your VASP **sends the transaction hash** or transaction ID to the VV Central Server.

Case 2: After executing the transaction, your VASP **does not send the transaction hash** or transaction ID to the VV Central Server.

4-2. **Cancel the transaction that has completed user verification.**

Case 1: For a halted virtual asset transfer, your VASP **sends an Error Report** to the VV Central Server.

Case 2: For a halted virtual asset transfer, your VASP **does not send an Error Report** to the VV Central Server.

<br />

## Test User Informations of RobotVASP

Here are virtual user informations that stored on Robot VASP for conducting test. Your VASP can conduct each case of withdrawal test using this information.

<Accordion title="User 1(Individual user who has completed KYC verification)">
  If all the information is entered correctly, the verification result must return `VERIFIED`.

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

### 1. Identify the Robot VASP

* **Conditions**
  * Your VASP must use the List VASP API(Enclave API).
* **Expected Result**
  * Your VASP can identify the Robot VASP using List VASP API.

<br />

### 2. Verify the Beneficiary's account information

* **Conditions**
  * Your VASP must use the User Account Verification API(Enclave API).
  * Your VASP must set the Robot VASP as the Beneficiary VASP using the information returned from the List VASP API.
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

### 3. Verify the Beneficiary's personal information

* **Conditions**
  * Your VASP must receive VERIFIED as a result of the User Account Verification API in order to conduct this test case.
  * Your VASP must use the User Verification API (Enclave API).
  * Your VASP must set the Robot VASP as the Beneficiary VASP using the information returned from the List VASP API.
* **Expected Result**
  * You can receive seven possible responses depending on how your VASP entered the test user information.
    * `VERIFIED`
    * `UNKNOWN-SYMBOL`
    * `UNKNOWN-ADDRESS`
    * `UNVERIFIED-KYC`
    * `MISMATCHED-NAME`
    * `UNAVAILABLE-INFORMATION`
    * `LACK-OF-INFORMATIONBLACKLISTED`
    <br />

### 4-1. Execute the transaction on the Blockchain Network

> 💡 Please Note:
>
> Upon receiving a VERIFIED response from both User Account Verification and User Verification, you must transfer the virtual assets to the Beneficiary's wallet address.
>
> If virtual assets are **not transferred** to the Robot VASP, **you will not be able to proceed with the deposit test.**
>
> If you send virtual assets to Robot VASP without receiving a VERIFIED response, the deposit test cannot be completed successfully.
>
> When conducting deposit and withdrawal tests for an XRP address, you must include the destination tag. Instructions on how to provide the destination tag can be found in the \[IVMS Guide]\(링크 추가 필요)  // 링크 추가 필요

**Case 1. Send the Transaction ID(Transaction Hash) to the VV Central Server after executing the transaction**

* **Conditions**
  * Your VASP must use the Report Transaction Result API to send the transaction hash to the VerifyVASP Central Server.
* **Expected Result**
  * Your VASP can confirm that the deposit has been reflected in the Robot VASP.

<Accordion title="How to use the Deposit Reflection Inquery API">
  **Method**: `GET`

  * **Endpoint**: `https://api.verifyvasp.xyz/vega/robot/v1.0/testnet/balance`
  * **Request Query**

  | Parameter Name | Type   | Description                                       | Example                                    |
  | -------------- | ------ | ------------------------------------------------- | ------------------------------------------ |
  | `vaspId`       | string | Originating VASP ID                               | 15952089931162058999                       |
  | `symbol`       | string | The symbol of the virtual asset to be transferred | ETH                                        |
  | `address`      | string | The address that is receiving the virtual asset   | 0xb0bFf9721871e22653358956cf59a5FdBF3D752F |

  * **Request Example**

  ```
  <https://api.verifyvasp.xyz/vega/robot/v1.0/testnet/balance?vaspId=15952089931162058999&symbol=ETH&address=0xb0bFf9721871e22653358956cf59a5FdBF3D752F>
  ```
</Accordion>

<br />

**Case 2. Do not send the Transaction ID (Transaction Hash) to the VV Central Server after executing the transaction**

* Conditions
  * Your VASP must not use the Report Transaction Result API after executing the virtual asset transfer transaction.
* Expected Result
  * Your VASP can receive a request regarding the Transaction Status Query API (VASP API) from the Robot VASP 10 minutes after executing the virtual asset transfer transaction.
  * Alternatively, your VASP can use the API below to trigger the Robot VASP to call the Check Transaction Status Simulation API.

<Accordion title="How to use the Deposit Reflection Inquery API">
  **Method**: `POST`

  * **Endpoint**: `https://api.verifyvasp.xyz/vega/robot/v1.0/action/tx/inquiry`
  * **Request Query**

  | Parameter Name     | Description                                                                                                                                       | Example                              |
  | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------ |
  | `verificationUuid` | Identifier to distinguish User Verification. You can receive this identifier as a response after calling the User Verification API (Enclave API). | ecb457e3-2307-4e72-8a42-16a3774e154b |

  * **Request Body Example**

  ```
  {
  "verificationUuid": "ecb457e3-2307-4e72-8a42-16a3774e154b"
  }
  ```
</Accordion>

<br />

### 4-2. Cancel the transaction that has completed user verification.

**Case 1. Send an error report to VV Central Server for a canceled virtual asset transfer transaction.**

* **Conditions**
  * Your VASP must use the Report Error API to send the canceled transaction to the VerifyVASP Central Server.
* **Expected Result**
  * The Robot VASP stops calling the Transaction Status Query API.
  * The verification result will be changed from VERIFIED to ERROR. Your VASP can confirm the change in the state of verification by using the Get Verification Result API or the List Verification Result API.

<br />

**Case 2. Do not send an error report to VV Central Server for a canceled virtual asset transfer transaction.**

* **Conditions**
  * Your VASP must not use the Report Error API after canceling the execution of a transaction that has completed verification.
* **Expected Result**
  * Robot VASP periodically calls Transaction Status Query API (VASP API) implemented by your VASP  for the related transaction. (up to 1 hour)