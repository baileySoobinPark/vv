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