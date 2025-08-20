---
title: Risk Assessment Via Chainalysis KYT API
api:
  file: TR_Enclave_API_Spec.yaml
  operationId: travelrule-Chainalysis-KYT
hidden: false
---
This API is used to assess the risk of wallet addresses and transactions using the Chainalysis KYT API. VASPs can leverage this to identify potential risks associated with specific addresses or transactions in advance, thereby preventing unauthorized or high-risk transfers.

***

## What is the Chainalysis KYT API?

Chainalysis' Know Your Transaction (KYT) API is a paid service that offers advanced risk assessment capabilities for virtual asset transactions. It provides more precise and sophisticated analysis compared to the free Sanction API and is particularly effective in assessing the risk of beneficiary wallet addresses and post-transfer transactions.

* To use the KYT API, you must first apply for a license through the [Chainalysis KYT Solution Page](https://www.chainalysis.com/solution/crypto-compliance/).
* After purchasing a license, you can issue an API key from the [KYT Console](https://kyt.chainalysis.com/). For detailed features and implementation, refer to the [API Reference Docs](https://docs.chainalysis.com/api/kyt/) and the [Official Developer Guide](https://docs.chainalysis.com/api/kyt/guides/#developer-portal).

## Implementation Guide

Both the Ordering VASP and the Beneficiary VASP may use the KYT API to assess risks associated with customer wallet addresses or transactions, as part of their regulatory compliance process. However, the user verification (`POST /verifications`) must be completed before invoking this API. Depending on the VASP’s role, the timing of API usage may differ as follows:

**\[Before Transaction Execution]**

* Before initiating a transfer, the Ordering VASP can assess the risk of the beneficiary’s wallet address.
* At this stage, usage of the KYT API by the Beneficiary VASP is restricted. If invoked, the API will return an `UNSUPPORTED-RISK-ASSESSMENT` error.

**\[After Transaction Execution]**

* The Ordering VASP can invoke the API after the transfer to evaluate the transaction risk.
* The Beneficiary VASP can assess the risk of an incoming transaction and take necessary actions based on the evaluation.

## Asynchronous API

The KYT API operates asynchronously. Final assessment results will be delivered via the Callback API.

## Prerequisites

1. **Set Enclave Environment Variables**\
   The issued API key must be set in the `VEGA_CHAINALYSIS_KYT_API_KEY` environment variable.
2. **Database Table Setup**\
   The Enclave database must include dedicated tables to store the results of KYT API responses. Refer to the [Enclave Database Setup](ref:database-setup-copy) page to create the necessary tables.

## API Specification