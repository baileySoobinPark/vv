---
title: List VASP API
excerpt: >
  This API provides a list of VASPs registered with VerifyVASP.

  The list is used during the withdrawal process to select the Beneficiary VASP.
  After retrieving the VASP list through this API, the vaspId field of the
  selected VASP can be used when initiating a verification request.


  ### VASP Eligibility Criteria
    The list includes your VASP and other VASPs that meet the following criteria:
    - The VASP is a member of VerifyVASP or another Travel Rule vendor.
    - The VASP is capable of handling asset transfers.
api:
  file: VN_Enclave_API_KR_Spec.yaml
  operationId: verifyName-List-VASP
hidden: false
---