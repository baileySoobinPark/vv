---
title: Risk Assessment Via Chainalysis KYT API
excerpt: >
  This API provides risk level assessment of wallet addresses and transactions
  by leveraging the Chainalysis KYT API. It enables VASPs to evaluate the
  potential risks associated with the wallet addresses and transactions they
  handle, helping to prevent and detect high-risk transactions.


  ### What is the Chainalysis KYT API?
    The Chainalysis Know Your Transaction (KYT) API is a paid service that provides advanced risk assessment capabilities for cryptocurrency transactions. The KYT API offers more detailed and accurate risk analysis compared to the Chainalysis Sanction API, with capabilities tailored to specific use cases such as:
    1. **Withdrawal Attempt**:Assess the risk level of a recipient's wallet address before initiating a withdrawal.
    2. **Transfer**:Evaluate the risk associated with a specific blockchain transaction after the asset transfer has been initiated.
    * To use the KYT API, you must sign up on the Chainalysis website and purchase a license. You can inquire about licensing directly through the [Chainalysis KYT page](https://www.chainalysis.com/solution/crypto-compliance/) or contact the VerifyVASP team for assistance in scheduling a meeting with the Chainalysis team.
    * Once the license is purchased, log in to the [Chainalysis KYT Console Site](https://kyt.chainalysis.com/) to generate your KYT API key. After logging in, navigate to "Help" > "Developers" > "API Keys" to obtain the key.
    * For comprehensive information about the KYT API, including its features and implementation guidelines, refer to the official resources below:
      * [Chainalysis KYT API Reference](https://docs.chainalysis.com/api/kyt/)
      * [Chainalysis KYT Developer Guide](https://docs.chainalysis.com/api/kyt/guides/#developer-portal)

  ### How to use KYT API
    Before calling the KYT API, the User Verification API (POST /verifications) must be completed. The behavior and results of the KYT API vary depending on whether the Transaction Report has been performed:

  **Before Submitting a Transaction Report**

  1. **Originatig VASP**
    * Evaluates the risk level of the Beneficiary’s wallet address.
    * This helps assess the potential risk of the recipient’s wallet address before initiating a withdrawal.

  2. ****
    * The Beneficiary VASP is restricted from using the KYT API at this stage.
    * Attempts to call the API will result in an UNSUPPORTED-RISK-ASSESSMENT error.

  **After Submitting a Transaction Report**

  1. **Originatig VASP**
    * Evaluates the risk level of the withdrawal transaction.
    * This provides insight into the potential risk associated with the transaction being sent.

  2. **Beneficiary VASP**
    * Assesses the risk level of the deposit transaction.
    * This enables the evaluation of the incoming transaction's risk and allows for appropriate risk management actions.

  By utilizing the Chainalysis KYT API, VASPs can enhance the security of
  virtual asset transactions. The Originating VASP can proactively assess the
  risk level of the Beneficiary's wallet address, while the Beneficiary VASP can
  evaluate the transaction's risk and take necessary actions accordingly.


  The KYT API operates asynchronously. You first register a target (such as a
  withdrawal attempt or transfer) for risk assessment with Chainalysis. The
  results of the risk evaluation are then delivered through the Callback API.


  ### Preparation Requirements

  1. Enclave Environment Variable Configuration: Set
  VEGA_CHAINALYSIS_KYT_API_KEY variable with the API key issued during the
  registration process.

  2. Database Table Setup:
    - The Enclave database must include a table to store Chainalysis KYT API call results.
    - Refer to the Chainalysis KYT Results table and the Chainalysis KYT Alerts table for table definitions.
api:
  file: 2025_05_02_TR_Enclave_API_Spec.yaml
  operationId: Screening_performRiskAssessmentViaChainalysisKYT
hidden: false
---