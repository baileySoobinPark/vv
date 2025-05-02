---
title: /VASP_DEFINED_PATH_VERIFY_USER_ACCOUNT
excerpt: >
  ## Verify User Account API


  This API must be implemented by your VASP to fulfill its role as a
  **Beneficiary VASP**.  

  Its primary purpose is to verify whether the beneficiary account
  address—designated by the Originator as the transfer destination—is owned by
  your VASP.


  By providing this endpoint, your VASP allows the **Enclave** to delegate
  verification tasks.  

  The Enclave will invoke this API upon receiving verification requests from the
  Originating VASP.


  ### Functional Requirements


  **1. Ownership Verification**  

  The API must validate whether the requested beneficiary account address exists
  in the VASP’s database.  

  Based on this verification, it should return the appropriate result.


  **2. Responding with Verification Results**  

  The API must return the verification result.  

  - If the account is owned by the VASP, the API must set the
  `accountVerificationResult` field value to `VERIFIED`.  

  - If the account is not recognized by the VASP, the API must set the
  `accountVerificationResult` field value to `DENIED`.


  **3. Failure Reason Specification**  

  When the `accountVerificationResult` field is set to `DENIED`, the `reason`
  field in the response must specify the reason for the failure.  

  The allowed values for this field are as follows:
api:
  file: 2025_05_02_VASP_API_Spec.yaml
  operationId: requestUserAccountVerification
hidden: false
---