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

Due to the complexity of the user verification process, this API operates asynchronously.  
The response only includes the unique `verificationUuid` for the request; the actual verification result will be returned later either:

* via a Callback API request containing the same UUID, or
* by querying the Get Verification Result API.

<br />

## Required Beneficiary Information

When requesting user verification to the beneficiary VASP, the originating VASP may specify which beneficiary information should be returned by using the <code>requiredBeneficiaryInfo</code> field in the request body.
This field allows the sender to indicate exactly which personal data of the beneficiary is needed for the verification process.

For example, if **NATURAL_PERSON_NAME** is specified, the beneficiary VASP should return the beneficiary’s full name.
If **ACCOUNT_NUMBER** is specified, the wallet address or account number should be included in the response.
Multiple attributes can be requested at once by separating them with commas (e.g., NATURAL_PERSON_NAME,ACCOUNT_NUMBER).

This mechanism helps both parties exchange only the necessary information for Travel Rule compliance while minimizing unnecessary data exposure. For more information about <code>requiredBeneficiaryInfo</code>, see [this section](ivms101-guide#ivms101-personal-information-code).

***

## API Specification
