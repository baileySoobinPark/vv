---
title: Risk Assessment Via Chainalysis Sanction API
excerpt: >
  This API provides risk level assessment of wallet addresses by leveraging the
  Chainalysis Sanction API. It enables VASPs to evaluate the potential risks
  associated with the wallet addresses they handle, helping to prevent
  unauthorized or high-risk transactions.


  ### What is the Chainalysis Sanction API??
    The Chainalysis Sanction API is a free service provided by [Chainalysis](https://www.chainalysis.com/) that assesses the risk associated with cryptocurrency wallet addresses. It allows organizations, including VASPs, to quickly identify high-risk addresses, such as those flagged for sanctions or other regulatory concerns. This API supports efficient compliance with anti-money laundering (AML) and sanction screening requirements, enhancing the security and reliability of virtual asset transactions.
      - To use the API, you must first obtain an API key by registering via the following link: [Chainalysis Public Sanction API Sign-Up](https://go.chainalysis.com/crypto-sanctions-screening.html).
      - For detailed information about the API, including usage guidelines, refer to the official documentation: [Chainalysis Public Sanction API Reference](https://public.chainalysis.com/docs/index.html)

  ### How to Use the Sanction API
    Before calling the Sanction API, the User Verification API (POST /verifications) must be completed. The Sanction API can be used in various scenarios, such as:
      * **By the Originating VASP**: After verifying the user via the User Verification API, the Originating VASP can evaluate the risk level of the Beneficiary's wallet address using the Sanction API before initiating an asset transfer.
      * **By the Beneficiary VASP**: The Beneficiary VASP can assess the risk associated with the Originator's wallet address to identify potential threats and take necessary precautions.

  ### Perparation Requirements

  1. **Enclave Environment Variable Configuration**: Set
  `VEGA_CHAINALYSIS_SANCTION_API_KEY` variable with the API key issued during
  the registration process.

  2. **Database Table Setup**: 
    - The Enclave database must include a table to store Chainalysis Sanction API call results.
    - Refer to the Chainalysis Sanction Results Table definition for implementation details.
api:
  file: 2025_05_02_TR_Enclave_API_Spec.yaml
  operationId: Screening_performRiskAssessmentViaChainalysisSanction
hidden: false
---