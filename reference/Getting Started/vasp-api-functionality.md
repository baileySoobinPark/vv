---
title: VASP API Functionality
excerpt: >-
  VerifyVASP provides a suite of API calls through the VASP's enclave server for
  Travel Rule compliance.
deprecated: false
hidden: true
metadata:
  robots: index
---
## User Address Verification API

### Overview

* This API is used by the originating VASP to be called by the beneficiary VASP to verify if the beneficiary VASP owns the address (account number or wallet address).
* The originating VASP can calls this user address verification API to pre-verify the beneficiary name and address (account number, wallet address) before calling the user verification API.

> 📘 Enclave Environment Variable
>
> Set the user address-verification API endpoint as **`VEGA_VERIFICATION_ACCOUNT_API_PATH`**

<br />

### Function(s)

* Verify that the address (wallet address) and the name belong to the VASP.

### Request/Response

* Request
  * KeyType, beneficiaryVaspId, symbol, and payload are passed to request a verification of the beneficiary's wallet address and name.
  * The wallet address and name fields need to be included in the payload in the IVMS101 messaging protocol format.
* Response
  * Returns a verification result whether the addresses of the symbol belong to (the beneficiary) VASP.
* For detailed API specifications, please refer to the link below.
* 카드 링크 추가

## User Verification API

### Overview

* This API is necessary for the originator VASP to be called by the beneficiary VASP.
* Upon verification request on the beneficiary, the beneficiary VASP needs to verify user information.

> 📘 Enclave Environment Variable
>
> Set the user verification API endpoint as **`VEGA_VERIFICATION_API_PATH`**

### Function(s)

* Verify the name of the beneficiary.
* Verify the beneficiary address (wallet address or account number).
* Verify whether the user has been certified by KYC
* Perform AML verification.
* Perform sanction screening on the originator.
  * VASP can use its own sanction-related service.
  * If the originator is on the sanction list after the risk has been checked, the verification result will return as "DENIED."

### Request/Response

* Request
  * Includes the information of Asset, Originator, Beneficiary in the IVMS101 message format.
* Response
  * Returns the result of the validity test on the beneficiary.
  * Returns additional information of beneficiary in \*\*\*\* IVMS101 format.
    * The beneficiary information initially requested is just a name and an account number entered by the originator. Thus, to satisfy the Travel Rules, additional information on the beneficiary (social security number, address, date of birth, place of birth, etc.) is returned.
  * For detailed API specifications, please refer to the link below.
* 카드 링크 추가