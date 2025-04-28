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
> Set the user address-verification API endpoint as **`VEGA_VERIFICATION_ACCOUNT_API_PATH`**.

<br />

### Function(s)

* Verify that the address (wallet address) and the name belong to the VASP.

### Request/Response

* **Request**
  * KeyType, beneficiaryVaspId, symbol, and payload are passed to request a verification of the beneficiary's wallet address and name.
  * The wallet address and name fields need to be included in the payload in the IVMS101 messaging protocol format.
* **Response**
  * Returns a verification result whether the addresses of the symbol belong to (the beneficiary) VASP.
* For detailed API specifications, please refer to the link below.
* 카드 링크 추가

## User Verification API

### Overview

* This API is necessary for the originator VASP to be called by the beneficiary VASP.
* Upon verification request on the beneficiary, the beneficiary VASP needs to verify user information.

> 📘 Enclave Environment Variable
>
> Set the user verification API endpoint as **`VEGA_VERIFICATION_API_PATH`**.

### Function(s)

* Verify the name of the beneficiary.
* Verify the beneficiary address (wallet address or account number).
* Verify whether the user has been certified by KYC.
* Perform AML verification.
* Perform sanction screening on the originator.
  * VASP can use its own sanction-related service.
  * If the originator is on the sanction list after the risk has been checked, the verification result will return as `"DENIED"`.

### Request/Response

* **Request**
  * Includes the information of Asset, Originator, Beneficiary in the IVMS101 message format.
* **Response**
  * Returns the result of the validity test on the beneficiary.
  * Returns additional information of beneficiary in \*\*\*\* IVMS101 format.
    * The beneficiary information initially requested is just a name and an account number entered by the originator. Thus, to satisfy the Travel Rules, additional information on the beneficiary (social security number, address, date of birth, place of birth, etc.) is returned.
  * For detailed API specifications, please refer to the link below.
* 카드 링크 추가

<br />

## Transaction Status Query API

### Overview

* This API is necessary for the beneficiary VASP to be called by the originating VASP.
* If the originating VASP is to send virtual assets through blockchain once it verifies the beneficiary via VerifyVASP, it must report the ID (TxHash) of the transaction to VerifyVASP.
* However, if the report is not performed normally, the API lets the beneficiary VASP check the transaction status on the blockchain.

> 📘 Enclave Environment Variable
>
> Set the transaction status query API endpoint as **`VEGA_VERIFICATION_TRANSACTION_API_PATH`**.

### Function(s)

* VASP needs to provide a transaction result (TxHash) of the verification UUIC it received.

### Request/Response

* **Request**
  * Includes verification UUID used in the user verification step
* **Response**
  * Returns transaction status and TxHash information corresponding to the verification UUID it received.
* For detailed API specifications, please refer to the link below.
* 카드 링크 추가

<br />

## Callback API

### Overview

* Some enclave APIs of VerifyVASP work asynchronously. A callback API is required to fetch the result.
* The user verification API is a typical example of an asynchronous API. Its instant response upon a request only includes the verificationUuid, not the final verification result.
* As the verification requests are asynchronously delivered to the Beneficiary VASP, the Originating VASP needs to implement this callback API and set a proper enclave environment variable to get the processing result notification.
* Implementation of callback API is optional but recommended: it is complicated to handle the asynchronous case without this API.

> 📘 Enclave Environment Variable
>
> Set the Callback API endpoint as **`VEGA_VERIFICATION_CALLBACK_API_PATH`**.

### Function(s)

* VASP needs to appropriately process the request along with the callbackType(VERIFICATION\_RESULT, TX\_REPORT, ERROR\_REPORT).
* A response should be returned as soon as possible.

### Request/Response

* **Request**
  * The result of an asynchronous API.
* **Response**
  * 200 OK Response
* For detailed API specifications, please refer to the link below.

<br />

## Decryption API for Encrypted Key

### Overview

* For the encryption key of the database enclave uses, you can use a better encryption key with this API.
* In an enclave, the encryption key used to encrypt sensitive information such as a private key or personal information is decrypted with this API.

> 📘 Enclave Environment Variable
>
> Set the decryption API endpoint corresponding to the encrypted key as **`VEGA_DECRYPT_API_ENDPOINT`**.

### Function(s)

* Provide a decrypted encryption key corresponding to the encryption key received once again.

### Response/Request

* **Request**
  * Includes an inputKey, an encryption key.
* **Response**
  * Returns the decrypted encryption key corresponding to the inputKey.
* For detailed API specifications, please refer to the link below.
* 카드 링크 추가

<br />

## Frontend development

* To comply with Travel Rule, an additional frontend (display side) development is required.
* At the very least, a display feature for getting the input of beneficiary VASP selection, beneficiary name, and his/her account number must be developed.