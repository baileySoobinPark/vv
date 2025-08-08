---
title: ' Withdrawal Scenario Test'
excerpt: >-
  This document explains the withdrawal scenario, including test cases and
  expected behaviors.
deprecated: false
hidden: false
metadata:
  robots: index
---
<Callout icon="💡" theme="default">
  ### Test Environment Limitations

  Withdrawal tests can only be performed on **Ethereum Sepolia** and **Ripple Testnet**.\
  Before starting, verify that asset transfer tests are supported in the selected environment and confirm the necessary information such as the Ordering VASP’s withdrawal account.
</Callout>

## Test Flow

Withdrawal scenario testing is performed in the following order:

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
      <div class="vasp-step-title">Check the Robot VASP information that will act as the Beneficiary VASP.</div>
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">2</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">User Account Verification: Send a verification request for the test beneficiary account information to the Robot VASP and confirm the expected response.</div>
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">3</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">User Verification: Send a verification request for the test beneficiary’s identity information to the Robot VASP and confirm the expected response.</div>
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">4-1</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">Proceed with a scenario where an asset transfer transaction is executed after verification. Test the following two sub-cases:</div>
      Case 1: Transaction report sent Case 2: Transaction report not sent
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">4-2</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">Proceed with a scenario where the transaction is canceled after verification. Test the following two sub-cases:</div>
      Case 1: Error report sent Case 2: Error report not sent
    </div>
  </li>
</ol>
`}</HTMLBlock>

<br />

## Test Data

The virtual user information used for the tests is as follows. This information is pre-registered in the Robot VASP (acting as the Beneficiary VASP) and can be successfully verified.

<Accordion title="RobotVASP KR" icon="fa-info-circle">
  Robot VASP in the KR region.

  ### Information

  * **url**: [https://api-kr.verifyvasp.xyz/vega/robot](https://api-kr.verifyvasp.xyz/vega/robot)

  * **name**: RobotVASP KR

  * **vaspId**: 4855741808338010592

  * **testnet**: Ethereum Sepolia, Ripple testnet
</Accordion>

<Accordion title="RobotVASP Global" icon="fa-info-circle">
  Robot VASP in the Global region.

  ### Information

  * **url**: [https://api.verifyvasp.xyz/vega/robot](https://api.verifyvasp.xyz/vega/robot)

  * **name**: RobotVASP

  * **vaspId**: 17104899575711300402

  * **testnet**: Ethereum Holesky, Ripple testnet
</Accordion>

<Accordion title="User 1(KYC-Verified Individual User)" icon="fa-info-circle">
  When all information below is entered correctly, the Robot VASP will return 'VERIFIED'.

  ### Personal information

  * **last name**: Robbins

  * **first name**: Taylor

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

  ### wallet address

  **'ETH' address**: `0x319E92715729c46869ed31d228f3b4f31e951450`

  **'XRP' address**: `rGFFufDwabHuPur9927p1EgBTcCBfsjtEU`

  **'XRP' destination tag**: `345678912`
</Accordion>

<Accordion title="User 3(KYC-Verified Corporate User)" icon="fa-info-circle">
  When all information, including the representative’s name, is entered correctly, the Robot VASP will return 'VERIFIED'. If all information is correct except for the representative’s name, the Robot VASP will return 'DENIED'.

  ### Personal information

  * **entity name**: Garrit Studio

    ### Represemtative name

  * **last name**: Clarke

  * **first name**: Jason

  ### wallet address

  **'ETH' address**: `0x26704Dc20d0ddF6cAa45b4D2b8AcB643015B951E`

  **'XRP' address**: `rGFFufDwabHuPur9927p1EgBTcCBfsjtEU`

  **'XRP' destination tag**: `234567891`
</Accordion>

<br />

## Test Cases

### 1. Robot VASP Identification Test

* **Condition**
  * Call the [VASP List API](ref:travelrule-list-vasp-ids) from the Enclave API running in the STG endpoint to retrieve Robot VASP information.
* **Expected Result**
  * The API response should include the Robot VASP’s information, including its vaspId.

<br />

### 2. User Account Verification Test

* **Condition**
  * Call the [User Account Verification API](ref:travelrule-enclave-user-account-verification) from the Enclave API. The Beneficiary VASP must be set to the Robot VASP. Perform verification tests for each of the three prepared test datasets.
* **Expected Result**
  * Confirm that the expected reason codes are returned for all possible `DENIED` cases as well as the normal `VERIFIED` scenario. Example: Send a verification request with data outside the test set to confirm `UNKNOWN-ADDRESS`.
    * `VERIFIED`
    * `UNKNOWN-SYMBOL`
    * `UNKNOWN-ADDRESS`
    * `UNVERIFIED-KYC`
    * `MISMATCHED-NAME`

<br />

### 3. User Verification Test

* **Condition**
  * Performed immediately after receiving a `VERIFIED` response from the User Account Verification test. Call the [User Verification API](ref:travelrule-encalve-request-user-verification) from the Enclave API. The Beneficiary VASP must be set to the Robot VASP.
* **Expected Result**
  * Confirm that the expected reason codes are returned for all possible `DENIED` cases as well as the normal `VERIFIED` scenario.
    * `VERIFIED`
    * `UNKNOWN-SYMBOL`
    * `UNKNOWN-ADDRESS`
    * `UNVERIFIED-KYC`
    * `MISMATCHED-NAME`
    * `UNAVAILABLE-INFORMATION`
    * `LACK-OF-INFORMATION`
    * `BLACKLISTED`
      <br />

### 4-1. On-Chain Transfer Transaction Execution Test

<Callout icon="💡" theme="default">
  ### Notes:

  If you receive a `VERIFIED` response in both the User Account Verification and User Verification tests, you must transfer the virtual asset to the beneficiary wallet address managed by the Robot VASP.\
  If no asset is transferred, or if assets are sent to the Robot VASP without a `VERIFIED` response, the withdrawal will not be processed and the subsequent deposit test cannot be performed.

  When testing with an XRP address, you must include a destination tag. Refer to the [IVMS101 Information Entry Guide](ivms101-guide#guidelines-for-wallet-address-entry) for instructions on how to include the destination tag.
</Callout>

**Case 1. After executing the transaction, send the Transaction ID (hash) to the VV Central Server**

* **Condition**
  * Call the [Report Transaction Result API]()  from the Enclave API to report the transaction result to the VV Central server.
* **Expected Result**
  * Call the Deposit Reflection Inquiry API to confirm that the asset has been deposited into the Robot VASP.

<Accordion title=" Deposit Reflection Inquiry API – How to Call">
  **Method**: `GET`

  * **Endpoint**: `https://api.verifyvasp.xyz/vega/robot/v1.0/testnet/balance`
  * **Query Parameters**

  | Parameter Name | Type   | Description                          | Example                                    |
  | -------------- | ------ | ------------------------------------ | ------------------------------------------ |
  | `vaspId`       | string | Ordering VASP ID                     | 15952089931162058999                       |
  | `symbol`       | string | Symbol of virtual asset              | ETH                                        |
  | `address`      | string | Address to receive the virtual asset | 0xb0bFf9721871e22653358956cf59a5FdBF3D752F |

  * **Request Example**

  ```
  <https://api.verifyvasp.xyz/vega/robot/v1.0/testnet/balance?vaspId=15952089931162058999&symbol=ETH&address=0xb0bFf9721871e22653358956cf59a5FdBF3D752F>
  ```
</Accordion>

<br />

**Case 2. Execute Transaction Without Reporting Transaction ID (Transaction Hash) to VV Central Server**

* **Condition**
  * After executing the virtual asset transfer transaction, do not report the execution result to the VV Central server.
* **Expected Result**
  * After 10 minutes, the Robot VASP will call the sender VASP’s [Check Transaction Status API]() to inquire about the transaction status.
  * Alternatively, you can call the Robot VASP’s Transaction Status Simulation API to instruct it to immediately start querying the transaction status.
  * Instructions for calling the Transaction Status Simulation API are provided below.

<Accordion title="Transaction Status Simulation API – How to Call">
  **Method**: `POST`

  * **Endpoint**: `https://api.verifyvasp.xyz/vega/robot/v1.0/action/tx/inquiry`
  * **Query Parameter**

  | Parameter Name     | Description                                                                                                                                | Example                              |
  | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------ |
  | `verificationUuid` | A unique identifier specifying the User Verification. You can obtain this value by calling the User Verification API from the Enclave API. | ecb457e3-2307-4e72-8a42-16a3774e154b |

  * **Request Body Example**

  ```
  {
  "verificationUuid": "ecb457e3-2307-4e72-8a42-16a3774e154b"
  }
  ```
</Accordion>

<br />

### 4-2. Cancel Transaction After Verification – Test

**Case 1. Cancel Virtual Asset Transfer Transaction and Report Error to VV Central Server**

* **Condition**
  * Do not execute the virtual asset transfer transaction, and instead call the [Report Error API]() to report the error to the VV Central server.
* **Expected Result**
  * The Robot VASP stops calling the Transaction Status Query API.
  * The verification result changes from `VERIFIED` to `ERROR`.
  * The test user can confirm the change by calling the [Get Verification Result API]() or [List Verification Result API]() from the Enclave API.

<br />

**Case 2. Cancel Virtual Asset Transfer Transaction Without Reporting Error to VV Central Server**

* **Condition**
  * The Robot VASP does not execute the asset transfer transaction and does not report the error to the VV Central server.
* **Expected Result**
  * The Robot VASP periodically calls the Transaction Status Query API implemented by the user’s VASP to check the transaction status.
  * The Robot VASP will continue calling the Transaction Status Query API for **up to 1 hour**.