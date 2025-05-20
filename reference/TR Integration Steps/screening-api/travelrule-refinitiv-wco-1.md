---
title: Risk Assessment Via Refinitiv WCO API
excerpt: >
  This API provides risk level assessment of wallet addresses and transactions
  by leveraging the Refinitiv WCO API.

  ### What is the Refinitiv WCO API?
    The Refinitiv World-Check One (WCO) API is a paid service designed for conducting risk assessments on individuals or entities using personal identification information. Risk assessment requests are referred to as 'case', which are uniquely identified by a caseSystemId assigned by Refinitiv. Additionally, the concept of 'groups' is used to manage case assignments, with each case belonging to a specific group. Reviewing and structuring these groups effectively is recommended before integrating the Refinitiv WCO API into your workflows.
    - To use the WCO API, you must create an account with Refinitiv and purchase a license. For inquiries or assistance in scheduling a meeting with the Refinitiv team, contact [Refinitiv World Check One](https://www.refinitiv.com/ko/products/world-check-kyc-screening/world-check-one-kyc-verification) directly or reach out to the VerifyVASP team for support.
    - Once you have purchased the license, log in to the [Refinitiv WCO Console Site](https://worldcheck.refinitiv.com/) to generate your API key. Navigate to "Admin Page > Users > [Your User]" to view the API Key and Secret in the user's details.
    - For more details about the Refinitiv WCO API and its capabilities, refer to the following resources:
      - [Refinitiv WCO API Overview](https://developers.lseg.com/en/api-catalog/customer-and-third-party-screening/world-check-one-api)
      - [Refinitiv WCO API Reference](https://developers.lseg.com/content/dam/devportal/en_us/product-docs/wc1-api/index.html)

  ### How to use WCO API
    Before calling the WCO API, the User Verification API (POST /verifications) must be completed. The Refinitiv WCO API allows VASPs to evaluate the risk level of the Originator/Beneficiary of a transaction. This enables VASP to perform additional risk assessments on whether the Beneficiary or Originator is a risky individual or organization. The WCO API also operates asynchronously, the results of the risk evaluation are delivered through the Callback API.

  ### Preparation Requirements

  1. **Enclave Environment Variable Configuration**:
    - Set `VEGA_REFINITIV_WCO_API_KEY` variable with the API key issued from the Refinitiv WCO Console Site. Navigate to Admin Page > Users > [Your User] to find the key in the user's details.
    - Set `VEGA_REFINITIV_WCO_API_SECRET` variable with the API secret retrieved from the same location as the API Key (Admin Page > Users > [Your User]).
    - Set `VEGA_REFINITIV_WCO_GROUP_ID` variable with the group ID. Create a group for managing cases in the Admin Page of the Refinitiv WCO Console Site. To retrieve the Group ID:
  2. **Use the SEQ-pre-groups**: Get my top-level groups request from the Group
  Information folder in the [Refinitiv WCO API Quick Start Postman
  Collection](https://developers.lseg.com/en/api-catalog/customer-and-third-party-screening/world-check-one-api/quick-start).

  3. **Set the API Key and API Secret as environment variables in Postman before
  making the request**.

  4. **Database Table Setup**:
    - All Refinitiv WCO API call logs are stored in the Enclave database.
    - You must create a dedicated table to store these records. Refer to the Refinitiv WCO Results Table definition for implementation details.
api:
  file: TR_Enclave_API_Spec.json
  operationId: travelrule-Refinitiv-WCO
hidden: false
---