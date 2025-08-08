---
title: RobotVASP Integration End-to-End Test
excerpt: >-
  Once the main integration tasks are completed, you can perform simulated
  deposit and withdrawal tests by using the Robot VASP provided by VerifyVASP as
  the counterparty VASP. This document explains how to verify the proper
  integration of major use cases through simulation tests.
deprecated: false
hidden: false
metadata:
  robots: index
---
## What is Robot VASP?

The Robot VASP is a **virtual VASP** within VerifyVASP’s Travel Rule integration test environment. VASPs that have completed their initial integration with the VASP API and Enclave can use the Robot VASP as a counterparty to perform simulated deposit and withdrawal tests with virtual users, ensuring the completeness of their implementation.

Using the Robot VASP, you can test scenarios such as:

1. Information verification for an **individual user** who has completed KYC.
2. Information verification for an **individual user** who has not completed KYC.
3. Information verification for a **corporate user** who has completed KYC.

Based on the results of each test case, you can also test transaction reporting (`Report Transaction`) or error reporting (`Report Error`) scenarios.

<br />

## Important Notes for Robot VASP Integration Testing

When using the Robot VASP and the testnet to send actual virtual assets for testing, please keep the following points in mind:

#### 1. Perform a withdrawal test first, then a deposit test

* After sending virtual assets to the Robot VASP in the withdrawal scenario, you can use those assets for the deposit test.

#### 2. Robot VASP’s assets are managed per receiving address (no hot/cold wallet separation)

* To reuse the assets sent in a withdrawal test for a deposit test, you must use the same receiving address from the withdrawal test as the sending address in the deposit test.
* The amount sent in the deposit test cannot exceed the total amount sent in the withdrawal test.

#### 3. Execute the asset transfer transaction only after receiving a VERIFIED user verification result

* This applies to both withdrawal and deposit tests.
* If you initiate an on-chain asset transfer to the Robot VASP (as the Beneficiary VASP) without receiving a VERIFIED result, you will not be able to recover the assets.

#### 4. When testing deposits/withdrawals for XRP addresses, always include the destination tag

* For instructions on specifying the destination tag in IVMS101 format, refer to the IVMS101 Data Entry Guide.

#### 5. Asset transfer tests are only available on Ethereum Sepolia, Ethereum Holesky, and Ripple Testnet

* Before starting the test, confirm whether asset transfers are supported in the respective environment and verify the VASP’s deposit account details and other required information.