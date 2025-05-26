---
title: Request Owner Verification API
excerpt: >
  VASPs can initiate the verification flow by calling this API to verify both
  the Originator and the Beneficiary.


  Owner verification is performed in the following two scenarios:
    1. **Post Verification**: The Beneficiary VASP performs Owner Verification after the Originating VASP executes the withdrawal transaction. This process is initiated by the Beneficiary VASP to verify whether the Originator and the Beneficiary represent the same entity.
    2. **Pre Verification**: Before executing the withdrawal transaction, the Originating VASP performs Owner Verification to confirm that the Beneficiary and the Originator represent the same entity.

  ### Owner Verification Result

  - The verification results for the following items — `ticker`, `network`,
  `address`, `tx_hash`, `dti`, `name`, `birth_date`, `date_of_incorporation`,
  and `organisation_identification` — can be returned as one of the following
  values: 
    - `MATCHED`
    - `MISMATCHED`
    - `SKIPPED`
api:
  file: VN_Enclave_API_Spec.yaml
  operationId: verifyname-owner-verification
hidden: false
---