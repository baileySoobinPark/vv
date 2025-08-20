---
title: List VASP API
api:
  file: VN_Enclave_API_Spec.yaml
  operationId: verifyName-List-VASP
hidden: false
---
Retrieves a list of all VASPs to which asset transfers are possible. When processing a customer’s asset transfer request, call this API to display the list to the customer, allow them to select a VASP, and then use the selected VASP’s `vaspId` value for verification requests.

***

## Inclusion Criteria for VASP List

The returned list includes your VASP as well as other VASPs that meet all of the following conditions:

* The VASP is a VerifyVASP member.
* Both VASPs have configured each other as eligible for asset transfer processing.

***

## API Specification