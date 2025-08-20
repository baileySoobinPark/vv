---
title: RobotVASP Integration End-to-End Test
excerpt: >-
  Once the main integration work is complete, you can perform mock deposit and
  withdrawal tests using the Robot VASP provided by VerifyVASP as the
  counterparty VASP. This document explains how to verify successful integration
  for key cases through mock testing.
deprecated: false
hidden: false
metadata:
  robots: index
---
## What is Robot VASP?

Robot VASP is a virtual VASP in the integration test environment provided by VerifyVASP.\
Once the initial integration of the VASP API and Enclave is complete, you can use Robot VASP as the counterparty VASP to conduct mock deposit and withdrawal tests with virtual users, ensuring the completeness of your implementation.

Using Robot VASP, you can test the following scenarios:

1. Information verification for individual users who have completed KYC
2. Information verification for individual users who have not completed KYC
3. Information verification for corporate users who have completed KYC

Based on the results of each test case, you can also test the **Result Report** (final verification result reporting) and **Transaction Hash Report** scenarios.

<br />

## Cautions when testing with Robot VASP

When performing asset transfer tests with Robot VASP and the testnet, please observe the following:

#### 1. VerifyName protocol is intended for same-person verification.

* The user information in Robot VASP must be identically stored in your VASP as well.
* When testing, use the same user information as stored in Robot VASP.

#### 2. Robot VASP manages virtual assets per receiving address (no hot/cold wallet separation).

* To receive assets back in a deposit test after a withdrawal test, make sure the deposit test’s sending address matches the receiving address used in the withdrawal test.
* The amount sent in the deposit test cannot exceed the total amount sent in the withdrawal test.

#### 3. On-chain asset transfers must only be performed for verifications with a final result of VERIFIED, for both withdrawal and deposit tests.

* If you create an on-chain asset transfer transaction to Robot VASP (as the receiving VASP) without obtaining a `VERIFIED` result, the transferred assets cannot be returned.

#### 4. For XRP address tests, remember to include the destination tag.

#### 5. Asset transfer tests can only be performed on Ethereum Sepolia, Ethereum Holesky, and Ripple Testnet.

* Before starting the test, check in advance whether asset transfer tests are available in your environment and gather all necessary information such as the VASP deposit account.