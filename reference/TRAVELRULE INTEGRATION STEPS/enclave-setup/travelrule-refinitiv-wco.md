---
title: Risk Assessment Via Refinitiv WCO API
api:
  file: TR_Enclave_API_Spec.yaml
  operationId: travelrule-Refinitiv-WCO
hidden: false
---
This API is used to assess the risk of wallet owners and transaction parties by integrating with the Refinitiv World-Check One (WCO) API. VASPs can proactively identify potential threats and prevent high-risk transfers.

***

## What is the Refinitiv WCO API?

The Refinitiv World-Check One (WCO) API is a paid service that performs risk screening based on identity information of individuals or legal entities. Each screening request is created as a "case", which is uniquely identified by a `caseSystemId` issued by Refinitiv. Each case belongs to a "group", and cases are managed by group. It is recommended to plan and organize your group structure before integrating the WCO API.

* To use the WCO API, you must first create a Refinitiv account and purchase a license.\
  You can initiate this process via the official [Official Refinitiv WCO page](https://www.refinitiv.com/ko/products/world-check-kyc-screening/world-check-one-kyc-verification) or by contacting the VerifyVASP team.
* After purchasing the license, you can log into the [WCO Console](https://worldcheck.refinitiv.com/) to retrieve your API Key. For detailed usage instructions, refer to the [WCO API Overview](https://developers.lseg.com/en/api-catalog/customer-and-third-party-screening/world-check-one-api) and the [WCO API Reference](https://developers.lseg.com/content/dam/devportal/en_us/product-docs/wc1-api/index.html).

## Implementation Guide

Using the WCO API, both the Ordering VASP and the Beneficiary VASP may perform risk assessments on the Originator or Beneficiary, based on their verified real-name identity information. This process can support regulatory compliance obligations.

\*Note: A user verification request (`POST /verifications`) must be completed prior to invoking this API.

<br />

## Asynchronous API

The WCO API operates asynchronously. Risk assessment results are delivered via the Callback API.

## Prerequisites

1. **Set Enclave Environment Variables**

* `VEGA_REFINITIV_WCO_API_KEY`: API key issued in the Refinitiv console.
* `VEGA_REFINITIV_WCO_API_SECRET`: API secret issued in the Refinitiv console.
* `VEGA_REFINITIV_WCO_GROUP_ID`: Group ID used to classify cases. You can create groups from the admin page of the Refinitiv console. To retrieve the Group ID, use the "Get my top-level groups" endpoint in the Refinitiv WCO API Quick Start Postman Collection under Group Information.

2. **Database Table Setup**\
   The Enclave database must include dedicated tables to store WCO API responses.
   Refer to the [Enclave Database Setup](ref:database-setup-copy) page for instructions on how to create and configure these tables.