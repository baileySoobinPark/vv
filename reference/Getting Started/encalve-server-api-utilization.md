---
title: Encalve Server API Utilization
excerpt: A guide to the purpose of the Enclave Server API and the order of calls.
deprecated: false
hidden: true
metadata:
  robots: index
---
## User Scenario

> 📘 A guide to using the enclave server API when a user requests for withdrawal via a VASP.

<br />

### User Withdrawal Request

<Image align="center" src="https://files.readme.io/efcc4daeb4e8f9a7c4bf85c64321c7b9ae2e303aa8dd15d94eab4cc194b41386-UserWithdrawalReqeustImage.avif" />

<br />

A user requests withdrawal via a VASP. Above is a reference image to understand the purpose of API.

* For example, when a user requests withdrawal via a VASP application,
* a user must enter the amount, the beneficiary VASP, the beneficiary address, and the beneficiary name.
  * Amount: How much virtual asset will be sent.
  * Beneficiary VASP: The VASP to which virtual asset is sent. Choose one from the list of receivable VASPs.
  * Beneficiary Address: The account number to which virtual asset is sent.
  * Beneficiary Name: The name of the beneficiary receiving the virtual asset.
* For convenience, VerifyVASP provides a list of Beneficiary VASPs.
  * By calling a `GET /vasps` API from a VASP’s backend, you can provide a list on a user’s screen.
  * VASP can manage the list itself and provide the list to the users (However, the VaspID must be consistent with the Vasp defined in the VASP list).
* Click withdrawal.
  * In the VASP backend, the verification process due to Travel Rule proceeds via VerifyVASP before the transaction of the actual withdrawal is sent to the blockchain.
  * Necessarily
    * Proceed with all verification processes on all of the information, including the information entered above such as Amount, Beneficiary VASP, Beneficiary Address, and Beneficiary Name.
    * In order to comply with the travel rules, it is necessary to check whether the account is owned by the exchange in advance, rather than transmitting all data of the sender and recipient from the beginning.
    * Use the `POST /v1/verifications/account` API to check if the wallet address is owned by the beneficiary VASP.
      * If the amount exceeds the legal standard, the wallet address and the recipient's name are delivered together for verification.
      * In this case, we do not perform KYC or Sanction Screening, but simply check if the account information owned by the exchange is correct.
      * If the wallet address of the recipient VASP is correct, the user verification process is performed to comply with the travel rules below.
    * You can check using `POST /verifications` API.
    * According to the IVMS101 message protocol, information about the beneficiary and originator should be sent together.
    * In the VASP backend, information about the originator should be offered in IVMS101 format.
  * Additionally, you can perform risk assessment with the wallet address or the personal information of the beneficiary/originator received from the other VASP.
    * You can assess the risk for the wallet address of counter party user using `POST /v1/risk-assessment/chainalysis-sanction` API.
    * You can assess the risk for the wallet address of counter party user or the risk for the transfer transaction using `POST /v1/risk-assessment/chainalysis-kyt` API.
    * You can assess the risk for the counter party user using `POST /v1/risk-assessment/refinitiv-wco` API.
* Whether to proceed with the transaction must be determined after the result of the verification process.
  * Although an ideal flow is yet to be decided, two options are plausible.
    * Proceeding actual transaction after VASP backend checks the result value by itself.
    * Display verification results to the users so that they can optionally proceed.
  * Proceed with the actual transaction in the VASP backend.
* Report withdrawal results.
  * In order for the VASP backend to finish the transaction, report (share) Transaction Hash to the beneficiary VASP.
    * Use `POST /v1/verifications/tx` API.
  * If you decided not to send transactions permanently because of an error, report (share) an error to the beneficiary VASP.
    * Use `POST /v1/verifications/error` API.
  * If a beneficiary VASP did not receive the transaction process report for a certain amount of time, a beneficiary VASP can ask for the transaction process status to the originating VASP. Use `POST /v1/verifications/tx/inquiry` API.
  * View verification history.
    * If you would like to view verification history proceeded during transactions via VASP in its backend,
      * Use `GET /v1/verifications` API.
    * If you need a screen easily approachable from VASP, compose one using the verification inquiry API above, which is provided by enclave.

<br />

### How to Process Multiple Withdrawal Requests in a Single Transaction

* If a VASP wants to proceed with multiple withdrawal requests as a single transaction to reduce fees, refer to the steps below.
  * VASP backend performs **a verification process** on each of the withdrawal requests as above N times.
    * Then a **verificationUuid** per withdrawal information is created.
  * Report a txHash and vout value (if exist), which is created by a single transaction transmission.
    * Report N times with the **same txHash and different vout** value for each verificationUuid corresponding to each withdrawal attempt.
* VerifyVASP does not automatically process multiple withdrawal requests as a single transaction.

<br />

### Enclave Server API Reference

* Enclave Server API Reference 링크 추가