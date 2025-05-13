---
title: Request User Verification API
excerpt: >
  The Originating VASP can request user verification by calling this Enclave
  API. This API verifies whether the personal information provided by the
  Originating VASP matches the account owner's information managed by the
  Beneficiary VASP.

  ### Asynchronous Operation

  This API operates asynchronously. Upon requesting a user verification, the
  response includes a verificationUuid, which uniquely identifies the requested
  verification. Once the Beneficiary VASP completes the user verification
  process, the result is delivered to the your VASP via the callback API, using
  the verificationUuid. The same UUID can also be used to retrieve the
  verification result at a later time if needed.
api:
  file: 2025_05_13_TR_Enclave_API_Spec.yaml
  operationId: travelrule-encalve-request-User-Verification
hidden: false
---