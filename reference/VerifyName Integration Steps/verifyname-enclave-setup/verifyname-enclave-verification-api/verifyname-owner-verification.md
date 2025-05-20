---
title: Request Owner Verification API
excerpt: >
  VASPs can initiate the verification flow by calling this API to verify both
  the Originator and the Beneficiary.

  Owner verification is performed in the following two scenarios:
    1. **Post Verification**: After the Originator withdraws from the Originating VASP and deposits to the Beneficiary address managed by the Beneficiary VASP, the Beneficiary VASP initiates owner verification to confirm whether the Beneficiary and the Originator are the same entity.
    2. **Pre Verification**: Before the Originator initiates a withdrawal from the Originating VASP, owner verification is performed to confirm that the Beneficiary at the Beneficiary VASP is indeed the Originator’s own account.

  ### Owner Verification Result

  - The result of the verification is returned in the response body of the API.

  - The verification results for the following items — ticker, network, address,
  tx_hash, dti, name, birth_date, date_of_incorporation, and
  organisation_identification — can be returned as one of the following values: 
    - `MATCHED`
    - `MISMATCHED`
    - `SKIPPED`
api:
  file: VN_Enclave_API_Spec.yaml
  operationId: verifyname-owner-verification
hidden: false
---