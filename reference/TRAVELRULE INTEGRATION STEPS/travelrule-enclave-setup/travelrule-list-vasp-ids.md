---
title: List VASP API
api:
  file: TR_Enclave_API_Spec.yaml
  operationId: travelrule-list-Vasp-ids
hidden: false
---
This API is used to retrieve a list of all VASPs that support Travel Rule verification.\
When a customer requests an asset transfer, your system can call this API to display a list of VASPs for the customer to choose from. You can then use the selected VASP's `vaspId` to perform the verification request.

***

## Criteria for VASP List Inclusion

The response will include your VASP as well as other VASPs that meet the following conditions:

* The VASP is a member of VerifyVASP or another Travel Rule protocol.
* Both parties have mutually configured each other as eligible VASPs for asset transfers.

***

## API Specification