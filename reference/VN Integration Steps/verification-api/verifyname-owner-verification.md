---
title: Request Owner Verification API
excerpt: >
  - VASP can use this API to verify the Beneficiary address or TxHash of
  Originator.

  - Your VASP can verify details using optional fields such as network, tag, dti
  etc.

  - Your VASP can call this API to verify the originator when your VASP detect
  the deposit of virtual assets or verify the beneficiary before executing the
  virtual asset transfer transaction. 


  ### Owner Verification Result

  - The result of the verification is returned in the response body of the API.

  - The verification results for the following items — ticker, network, address,
  tx_hash, dti, name, birth_date, date_of_incorporation, and
  organisation_identification — can be returned as one of the following values: 
    - MATCHED
    - MISMATCHED
    - SKIPPED
api:
  file: VN_Enclave_API_Spec.yaml
  operationId: verifyname-owner-verification
hidden: false
---