---
title: Request User Verification API
api:
  file: TR_Enclave_API_Spec.yaml
  operationId: travelrule-encalve-request-User-Verification
hidden: false
---
This API is used by the ordering VASP to initiate a user verification request to the beneficiary VASP. If the request is successfully received by the VerifyVASP Central Server, a unique `verificationUuid` will be returned in the response to identify the request.

***

## Asynchronous API

Due to the complexity of the user verification process, this API operates asynchronously.\
The response only includes the unique `verificationUuid` for the request; the actual verification result will be returned later either:

* via a Callback API request containing the same UUID, or
* by querying the Get Verification Result API.

***

## API Specification