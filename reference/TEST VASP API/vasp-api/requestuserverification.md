---
title: Verify User API
excerpt: >
  This API must be implemented by your VASP to fulfill its role as a Beneficiary
  VASP. Its primary purpose is to verify the consistency of the originator user
  provided Beneficiary's personal information against the Beneficiary VASP
  managing data, while ensuring compliance with KYC/AML and sanction screening
  requirements.

  By providing this endpoint, your VASP allows the Enclave to delegate
  verification tasks. The Enclave will invoke this API upon receiving
  verification requests from the Originating VASP.


  ## Functional Requirements

  ### 1. Personal Information Verification
    * The API must validate whether the personal information provided by the Originating VASP matches the data managed by the Beneficiary VASP.
    * Fields to be matched include:
        * Name: Verify that the Beneficiary's name matches the existing records.
        * Address: Verify that the account number or wallet address matches the existing records.

  ### 2. Compliance Assurance
    The verification process must ensure regulatory compliance by performing the following checks and validations for the Beneficiary:
      * **KYC Validation**: Check if the Beneficiary has completed Know Your Customer (KYC) authentication.
      * **AML Validation**: Confirm the Beneficiary complies with Anti-Money Laundering (AML) requirements.
    * **STR Monitoring and Sanction Screening**: Perform Suspicious Transaction Reporting (STR) analysis and sanction screening for the Originator.
    * **Additional Filtering**: Your VASP may conduct extra filtering or verification of the provided personal information for both the Originator and Beneficiary
  ### 3. Responding to Originating VASP Data Requests
    Your VASP (Beneficiary VASP) must provide all information requested by the Originating VASP in the requiredBeneficiaryInfo object during the verification process, by filling and returning the ivms101 object with the data.
    * If your VASP does not have or cannot provide the requested information, the API must set verificationResult to `DENIED` and reason to `UNAVAILABLE-INFORMATION`.
    * Only the information explicitly requested in requiredBeneficiaryInfo should be filled. Any fields not requested by the Originating VASP must remain empty.
    * The beneficiary account number (or wallet address) must be returned without modification. Altering the wallet address may result in incorrect deposits. If the deposit address is invalid or incorrect, the API must set verificationResult to `DENIED`.
  ### 4. Responding with Additional Beneficiary Information and Verification
  Results
    The API must return the verification result.
    * After verifying both the Originator and Beneficiary information (including addresses and personal details), the API must return the result field as `VERIFIED` if the provided data matches the beneficiary side records and there are no exceptional reasons to deny the asset transfer.
    * If the provided information is insufficient or does not match the details on the Beneficiary's side, or if the account address or its owner is considered high-risk for asset transfer, the API must return the result field as `DENIED`.
    Upon successful user verification, the API must return additional personal information about the Beneficiary in the IVMS101 messaging format.
  ### 5. Failure Reason Specification
    When the result field is set to `DENIED`, the reason field in the response must specify the reason for the failure. The allowed values for this field are as follows:
api:
  file: 2025_05_02_VASP_API_Spec.yaml
  operationId: requestUserVerification
hidden: false
---