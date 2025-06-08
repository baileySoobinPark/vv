---
title: ' 출금 시나리오 테스트'
excerpt: '본 문서 가이드를 통해 출금 시나리오를 확인하고 테스트 케이스와 기대 동작을 확인할 수 있습니다. '
deprecated: false
hidden: true
metadata:
  robots: index
---
> 💡 테스트 환경 제약사항
>
> 출금 테스트는 **Ethereum Sepolia** 및 **Ripple Testnet**에서만 수행 가능합니다. 테스트를 시작하기에 앞서, 해당 환경에서 자산 전송 테스트가 가능한지 여부와 VASP 출금 계좌 등 필요한 정보를 미리 확인하십시오.

## 테스트 순서

출금 시나리오 테스트는 다음과 같은 순서로 진행됩니다.

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
      <div class="vasp-step-title">상대 VASP로 동작할 Robot VASP정보를 확인합니다.</div>
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">2</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">User Account Verification: 테스트용 수신 계정 정보에 대한 검증 요청을 Robot VASP에게 전송한 뒤, 기대 응답의 발생 여부를 확인합니다.</div>
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">3</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">User Verification: 테스트용 수신자 정보에 대한 검증 요청을 Robot VASP에게 전송한 뒤, 기대 응답의 발생 여부를 확인합니다.</div>
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">4-1</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">검증 이후 자산 전송 트랜잭션을 실행하는 시나리오로 진행합니다. 아래 두 하위 케이스에 대해 테스트합니다.</div>
      (Case 1)트랜잭션 Report 전송 (Case 2)트랜잭션 Report 미전송
    </div>
  </li>
  <li class="vasp-step-item">
    <div class="vasp-step-badge">4-2</div>
    <div class="vasp-step-content">
      <div class="vasp-step-title">검증 이후 트랜잭션을 취소하는 시나리오로 진행합니다. 아래 두 하위 케이스에 대해 테스트합니다. </div>
      (Case 1)Error Report 전송 (Case 2)Error Report 미전송
    </div>
  </li>
</ol>
`}</HTMLBlock>

<br />

## 테스트 데이터

테스트의 사용할 가상의 사용자 정보는 아래와 같습니다. 이 정보는 수신 VASP로 동작하는 Robot VASP에 미리 저장되어 있어 검증이 가능한 정보입니다.

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