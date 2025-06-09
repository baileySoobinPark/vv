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

<Accordion title="User 1(KYC 인증을 완료한 개인 사용자)">
  아래의 정보를 정확하게 입력하였다면 Robot VASP가 `VERIFIED`를 반환합니다.

  ### 개인 정보

  * **성**: Robbins

  * **이름**: Taylor

  ### 지갑 주소

  **'ETH' address**: `0xFa230E9cCAF5e382539147294d7965Eeccbbfa5c`

  **'XRP' address**: `rGFFufDwabHuPur9927p1EgBTcCBfsjtEU`

  **'XRP' destination tag**: `123456789`
</Accordion>

<Accordion title="User 2(KYC 인증을 완료하지 않은 개인 사용자)">
  아래의 정보를 정확하게 입력하여도 Robot VASP가 `DENIED`를 반환합니다.

  ### 개인 정보

  * **성**: Cook

  * **이름**: Ethan

  ### 지갑 주소

  **'ETH' address**: `0x319E92715729c46869ed31d228f3b4f31e951450`

  **'XRP' address**: `rGFFufDwabHuPur9927p1EgBTcCBfsjtEU`

  **'XRP' destination tag**: `345678912`
</Accordion>

<Accordion title="User 3(KYC 인증을 완료한 기업 사용자)">
  대표 이름을 포함한 모든 정보를 정확하게 입력한 경우 'VERIFIED'를 반환합니다. 그러나 다른 모든 정보를 정확하게 입력하였어도 대표 이름 정보에 오류가 있는 경우, `DENIED`를 반환합니다.

  ### 개인 정보

  * **회사 이름**: Garrit Studio

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

### 1. Robot VASP Identification Test

* **조건**
  * STG Endpoint로 구동한 Enclave API 중 [List VASP API](ref:travelrule-list-vasp-ids-1)를 호출하여 Robot VASP 정보를 조회합니다.
* **기대 결과**
  * API 호출 응답에서 Robot VASP 정보를 확인할 수 있습니다. (vaspId 포함)

<br />

### 2. User Account Verification Test

* **조건**
  * Enclave API 중 [User Verification API](ref:travelrule-encalve-request-user-verification-1)를 호출하여 진행합니다. 수신 VASP로 반드시 Robot VASP를 설정합니다.
  * 테스트 데이터 3건에 대한 검증 테스트를 각각 수행합니다.
* **Expected Result**
  * 테스트 데이터를 기준으로 정상 검증(`VERIFIED`) 시나리오를 포함하여 발생 가능한 아래 모든 `DENIED` 케이스에 대해 기대한 사유 코드가 반환되는지 확인합니다. 예를 들어, 테스트 데이터 외의 임의의 데이터로 검증을 요청하여 `UNKNOWN-ADDRESS`가 발생하는지 확인할 수 있습니다.
    * `VERIFIED`
    * `UNKNOWN-SYMBOL`
    * `UNKNOWN-ADDRESS`
    * `UNVERIFIED-KYC`
    * `MISMATCHED-NAME`
    * `UNAVAILABLE-INFORMATION`
    * `LACK-OF-INFORMATION`
    * `BLACKLISTED`

<br />

### 3. User Verification Test

* **조건**
  * 사용자 주소 검증에서 VERIFIED 응답을 받은 직후 이어서 진행합니다.
  * Enclave API 중 [User Verification API](ref:travelrule-encalve-request-user-verification-1)을 호출하여 테스트합니다. 수신 VASP로 반드시 Robot VASP를 설정합니다.
* **기대 결과**
  * 테스트 데이터를 기준으로 정상 검증(`VERIFIED`) 시나리오를 포함하여 발생 가능한 아래 모든 `DENIED` 케이스에 대해 기대한 사유 코드가 반환되는지 확인합니다. 예를 들어, 테스트 데이터 외의 임의의 데이터로 검증을 요청하여 `UNKNOWN-ADDRESS`가 발생하는지 확인할 수 있습니다.
    * `VERIFIED`
    * `UNKNOWN-SYMBOL`
    * `UNKNOWN-ADDRESS`
    * `UNVERIFIED-KYC`
    * `MISMATCHED-NAME`
    * `UNAVAILABLE-INFORMATION`
    * `LACK-OF-INFORMATIONBLACKLISTED`
    <br />

### 4-1. 온체인 전송 트랜잭션 실행 Test

> 💡 Please Note:
>
> Upon receiving a VERIFIED response from both User Account Verification and User Verification, you must transfer the virtual assets to the Beneficiary's wallet address.
>
> If virtual assets are **not transferred** to the Robot VASP, **you will not be able to proceed with the deposit test.**
>
> If you send virtual assets to Robot VASP without receiving a VERIFIED response, the deposit test cannot be completed successfully.
>
> When conducting deposit and withdrawal tests for an XRP address, you must include the destination tag. Instructions on how to provide the destination tag can be found in the [IVMS101 Guide](ref:ivms101-guide).

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