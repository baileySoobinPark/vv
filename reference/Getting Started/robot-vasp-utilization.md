---
title: Robot VASP Utilization
deprecated: false
hidden: true
metadata:
  robots: index
---
You can use Robot VASP to simulate originator / beneficiary verification and deposit / withdrawal process through VerifyVASP.

* You can check whether the implementation related to travel rules works as intended in the withdrawal scenario by testing with Robot VASP as Beneficiary VASP.
* You can check whether the implementation related to travel rules works as intended in the deposit scenario by testing with Robot VASP as Originating VASP.

<br />

## User Information on Robot VASP

The following user details are test users hosted on Robot VASP. These are designed to cover most of the test cases needed to verify the VerifyVASP travel rule API implementation on your environment. Use the following user details as originator or beneficiary details when simulating withdrawals or deposits with Robot VASP as your counterparty.

<Accordion title="User 1(KYC verified natural person)">
  This user can be assumed as a natural person who has already completed KYC verification. Therefore, if all information is entered correctly, you can receive VERIFIED verification results.

  * Personal Information

    * Last name : Robbins

    - First name : Taylor

  - Wallet Addresses

    * 'ETH' address: '0xFa230E9cCAF5e382539147294d7965Eeccbbfa5c'

    * 'XRP' address: 'rGFFufDwabHuPur9927p1EgBTcCBfsjtEU'

    * 'XRP' destination tag: '123456789'
</Accordion>

<Accordion title="User 2(KYC unverified natural person)">
  This user can be assumed as a natural person who has not completed KYC verification. Therefore, if all information is entered correctly, you can receive DENIED (UNVERIFIED-KYC) verification results.

  * Personal Information

    * Last name : Cook

    * First name : Ethan

  * Wallet Addresses

    * 'ETH' address: '0x319E92715729c46869ed31d228f3b4f31e951450'

    * 'XRP' address: 'rGFFufDwabHuPur9927p1EgBTcCBfsjtEU'

    * 'XRP' destination tag: '345678912'
</Accordion>

<Accordion title="User 3(KYC verified legal person)">
  This user can be assumed as a legal person who has already completed KYC verification. Therefore, when requesting user verification, the representative name must also be entered to receive VERIFIED verification results.

  * Personal Information

    * Legal person name : Garrit Studio

    * Representative information

      * Last name : Clarke

      * First name : Jason

  * Wallet Addresses

    * 'ETH' address: '0x26704Dc20d0ddF6cAa45b4D2b8AcB643015B951E'

    * 'XRP' address: 'rGFFufDwabHuPur9927p1EgBTcCBfsjtEU'

    * 'XRP' destination tag: '234567891'
</Accordion>

<br />

## Testing VerifyVASP TravelRule API Implementation Using Robot VASP

You can test both withdrawal and deposit scenarios from your VASP using Robot VASP. Using the blockchain testnets, virtual asset transfers can be tested in a more realistic setting, and your implementation of VerifyVASP TravelRule API can also be verified along with virtual asset transfers on a blockchain. Currently, the blockchain testnets supported by Robot VASP are **Ethereum Holesky** and **Ripple testnet**. The wallet addresses of the Robot VASP's virtual users listed above are live on the testnets.

<br />

When testing the transfer of virtual assets using Robot VASP on testnets, keep the following in mind.

1. You must perform a test on withdrawal scenario before you test a deposit scenario.

* First, you need to run a withdrawal test that sends the virtual asset to the Robot VASP so that the Robot VASP have some virtual asset balance.
* Only then you can test the deposit scenario where Robot VASP sends virtual asset to your VASP.

2. Robot VASP does not have hot or cold wallets, therefore it uses the same wallet address when sending or receiving virtual assets (VA)

* In other words, originator address (hosted by Robot VASP) in the deposit test should be the same as the beneficiary address (hosted by Robot VASP) in the withdrawal test.
* The quantity of VA you can request during the deposit test cannot exceed the total VA quantity you sent to the robot VASP address during the withdrawal test.

3. User verification process must be performed and results must be 'VERIFIED' before corresponding virtual asset transfers are conducted for both withdrawal and deposit tests.

* Only after receiving the VERIFIED result from the user verification process, the blockchain transaction corresponding to that verification should be sent.
* If virtual assets are transferred to Robot VASP without VERIFIED result from user verification during the withdrawal cycle, virtual assets would not be returned by the Robot VASP for the corresponding deposit cycle.

4. When testing deposits and withdrawals for XRP addresses, do not forget to include the destination tag.

* Please refer to [IVMS101 Message Format Guide]()  to find out how to input the destination tag in ivms101 format.

<br />

## Withdrawal scenario test

The withdrawal scenario can be validated by having the Robot VASP act as the Beneficiary VASP. When invoking Enclave API, **enter beneficiaryVaspId to VASP ID of Robot VASP**. The VASP ID of the Robot VASP can be found by calling [VASP List Lookup API]()  of enclave.

It is recommended that withdrawal tests be performed in the following order:

### 1. Account verification

* Call [Account Verification API]()  of enclave by specifying the virtual user of Robot VASP as a beneficiary.
* Please refer to \[How to test for each verification result] at the bottom.

<br />

### 2. User verification

* Call [User Verification API]()  of enclave by specifying a virtual user of Robot VASP as beneficiary.
* Unlike [Account Verification API]() , it is processed in an asynchronous way and a verification uuid that can uniquely identify a user verification request is returned in the response body.
* The user verification result is delivered through a separate [Callback VASP API]() .
* Please refer to [How to test for each verification result]()  at the bottom.

<br />

### 3. Withdrawal of virtual assets

* Transfer **the same amount of virtual assets** on the testnet blockchain that has been `VERIFIED` in the previous step 2 above.
* Currently, the blockchain networks supported by Robot VASP are **Ethereum Holesky** and **Ripple testnet**.
* The **to address** of the blockchain transaction must be the same address as the beneficiary wallet address of the `VERIFIED` user verification corresponding to the transaction.

<br />

### 4. Transaction result report

* If the withdrawal transaction at the previous step 3 was successfully transmitted to the blockchain node, the transaction hash or transaction ID will probably be returned. This should be reported immediately to the beneficiary VASP (i.e. Robot VASP) using [Transaction Results Report API]() .
* In the request body of the call, the verification uuid of the user verification process corresponding to the transaction should be placed.
* Once Robot VASP successfully receives the transaction result report, the deposit will be reflected in the corresponding beneficiary account.
* You can use the following API to check whether the deposit has been reflected properly in the Robot VASP.

<Accordion title="Deposit reflection inquiry API">
  **GET** [https://api.verifyvasp.xyz/vega/robot/v1.0/testnet/balance](https://api.verifyvasp.xyz/vega/robot/v1.0/testnet/balance)

  Query parameters example

  ```
  1. vaspId : "15952089931162058999", // vaspId of your (originating) VASP
  2. symbol : "ETH", // transferred virtual asset
  3. address : "0xb0bFf9721871e22653358956cf59a5FdBF3D752F" // beneficiary address (deposit address)
  ```
</Accordion>

<br />

### 5. Error situation report

If there is a problem during the withdrawal phase at the step 3 above, or if your VASP eventually decided not to transfer the assets corresponding to the verification, your VASP should report it to the beneficiary VASP (i.e. Robot VASP) using the [Error Report API]() .

Upon successfully receiving an error status report, Robot VASP will no longer invoke [Transaction Status Query API]()  for that verification.

If you do not send a transaction report or an error status report for the VERIFIED user verification, Robot VASP will periodically invoke the [Transaction Status Query API]()  for that verification until up to an hour later.

<br />

### 6. Transaction status query

* If you do not send the transaction report (step 4) after transferring the withdrawal transaction to the blockchain (step 3), Robot VASP cannot reflect the deposit to its user since it does not know which user verification result corresponds to which deposit transaction detected on blockchain.
* Therefore, if a transaction report does not arrive within a specific time (10 minutes) for a deposit transaction detected through the blockchain, Robot VASP begins to query the transaction status to the originating VASP (i.e. your VASP) using [Transaction status query API]() .
* In other words, if you would like to test whether your VASP's transaction status query API is working properly, you can wait for a specific time (10 minutes) without sending a transaction report after sending a withdrawal transaction.
* If you want to be called immediately without waiting for 10 minutes, call the following Robot VASP API:

<Accordion title="Transaction status query simulation API">
  **POST** [https://api.verifyvasp.xyz/vega/robot/v1.0/action/tx/inquiry](https://api.verifyvasp.xyz/vega/robot/v1.0/action/tx/inquiry)

  ```json
    {
    "verificationUuid": "ecb457e3-2307-4e72-8a42-16a3774e154b" // uuid of previous successful verification
    }
  ```
</Accordion>

<br />

### How to test for each verification result

To test various cases in account validation or user validation results against Robot VASP, please refer to the following:

1. `VERIFIED`

* Invoke verification API with the correct name and wallet address of the Robot VASP user.
* When testing with a legal person, the name of the representative must be entered correctly. Refer to \[IVMS101 Message Format Guide] (../reference/ivms101/ivms101-1.md#personal-info)) for instructions on entering the name of the representative.

2, `UNKNOWN-SYMBOL`

* Currently, Robot VASP does not support other assets except ETH and XRP.
* Therefore, if you enter a symbol other than the above two assets, UNKNOWN-SYMBOL will be returned as a result.

3. `UNKNOWN-ADDRESS`

* If you enter an address other than the wallet address of the Robot VASP user as the beneficial wallet address, UNKNOWN-ADDRESS will be returned as a result.

4. `UNVERIFIED-KYC` (this is not applicable for user account verification API)

* The Robot VASP user Ethan Cook is assumed to be not KYC verified.
* Therefore, if you enter the information of Ethan Cook as beneficiary, UNVERIFIED-KYC will be returned as a result.

5. `MISMATCHED-NAME`

* If you enter the correct wallet address and incorrect user name for Robot VASP user, MISMATCHED-NAME will be returned as a result.

6. `UNAVAILABLE-INFORMATION` (this is not applicable in user account verification API)

* When invoking user verification API for a natural person, if you enter any Personal Data Fields other than the following fields in the requiredBeneficiaryInfo field, UNAVAILABLE-INFORMATION will be returned as a result.
  * NATURAL\_PERSON\_NAME
  * ACCOUNT\_NUMBER

7. `LACK-OF-INFORMATION` (this is not applicable for user account verification API)

* The `LACK-OF-INFORMATION` error is returned when originator verification in the beneficiary VASP is not possible due to the lack of personal information about the originator.
* If name or dateAndPlaceOfBirth element is not found in originator information when invoking user verification API, `LACK-OF-INFORMATION` will be returned as a result.

8. `BLACKLISTED` case (not returned in account verification)

* The BLACKLISTED' error is returned when the originator is determined to be an undesirable person (e.g. being on a deny list) by the beneficiary VASP.
* If you enter originator information as follow when invoking user verification API, `BLACKLISTED` will be returned as a result.
  * first name : Pablo
  * last name : Escobar
  * date of birth : 1949-12-01
  * place of birth : Colombia

<br />

## Deposit scenario test

The deposit scenario can be validated by having the Robot VASP act as the Originating VASP. By calling the APIs below, you can have the Robot VASP invoke a series of Travel Rule APIs as if someone was trying to send virtual assets from Robot VASP to your VASP (i.e. making deposit to your VASP). In the deposit scenario test, invoke APIs with **your VASP ID as beneficialVaspId**.

It is recommended for deposit tests to be performed in the following order:

### 1. Account verification

* You can call the following Robot VASP API to request that Robot VASP send an account verification to your VASP.

<Accordion title="Account Verification Simulation API">
  **POST** [https://api.verifyvasp.xyz/vega/robot/v1.0/action/verifications/account](https://api.verifyvasp.xyz/vega/robot/v1.0/action/verifications/account)

  ```json
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

<br />

### 2. User verification

* You can call the following Robot VASP API to request that Robot VASP send a user verification to your VASP.

<Accordion title="User Verification Simulation API">
  **POST** [https://api.verifyvasp.xyz/vega/robot/v1.0/action/verifications](https://api.verifyvasp.xyz/vega/robot/v1.0/action/verifications)

  ```json
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

<br />

### 3. Deposit virtual assets(withdrawal from Robot VASP)

* You can call the following Robot VASP API to request Robot VASP perform virtual asset transfer to your VASP.
* User verification must be performed before requesting asset transfer. Robot VASP only performs withdrawals corresponding to the VERIFIED user verification.
  * The accountNumber of the beneficiary must be the address to which Robot VASP should send virtual assets.
  * Since the virtual assets sent from Robot VASP will be from the balance of what you have sent during withdrawal test, the originator's accountNumber used in this flow should be same as the beneficiary wallet address on Robot VASP you used during the withdrawal test.
* The type and quantity of transferred assets are the same as the 'assetInfo' entered in the corresponding user verification.
  * The deposit amount cannot exceed the total amount previously transferred during the withdrawal test. In other words, Robot VASP can send back only the amount it received.

<Accordion title="Robot VASP Withdrawal Request API">
  **POST** [https://api.verifyvasp.xyz/vega/robot/v1.0/action/withdrawal](https://api.verifyvasp.xyz/vega/robot/v1.0/action/withdrawal)

  ```json
    {
    "verificationUuid": "ecb457e3-2307-4e72-8a42-16a3774e154b", // uuid of previous successful verification
    "omitTxReport": false // (optional) whether the transaction report is sent or not after withdrawal
  }
    
  ```
</Accordion>

<br />

### 4. Transaction report

* Robot VASP is implemented to automatically send a transaction report after performing asset transfer unless a special option is used.
* Therefore, transaction report will be delivered to your VASP through [Callback VASP API]()  within a few seconds after the actual transaction is sent.
* If you want to simulate the Robot VASP not to report the transaction after transferring the asset, set `omitTxReport` to true when invoking the withdrawal request API in step 3.
* If you want to test the transaction report feature regardless of whether the actual asset is transferred or not, you can force Robot VASP to report the transaction by calling the following API.

<Accordion title="Robot VASP Transaction Reporting Simulation API">
  **POST** [https://api.verifyvasp.xyz/vega/robot/v1.0/action/verifications/tx](https://api.verifyvasp.xyz/vega/robot/v1.0/action/verifications/tx)

  ```json
  {
    "verificationUuid": "f02081b4-1837-41c0-a96c-221399db46d2", // previous successful verification
    "txHash": "0xaaa042c0632f4d44c7cea978f22cd02e751a410e"
  }
    
  ```
</Accordion>

### 5. Error situation report

* Robot VASP does not automatically send an error report unless asset transfer fails.
* If you want to test the error report feature, you can force Robot VASP to report the error situation by calling the following API.

<Accordion title="Robot VASP Error Situation Reporting Simulation API">
  **POST** [https://api.verifyvasp.xyz/vega/robot/v1.0/action/verifications/error](https://api.verifyvasp.xyz/vega/robot/v1.0/action/verifications/error)

  ```json
  {
    "verificationUuid": "f02081b4-1837-41c0-a96c-221399db46d2", // previous successful verification
    "result": "DENIED",
    "reason": "USER-CANCELED",
    "message": "User canceled"
  }
  ```
</Accordion>

<br />

### 6. Transaction status query

* Robot VASP is designed to automatically report transaction when withdrawal is made. Therefore, it is very unlikely that transaction report will not be delivered to your VASP.
* Nevertheless, if you want to query the transaction status to Robot VASP, you can call the [Transaction Status Inquiry API]()  of your enclave server.