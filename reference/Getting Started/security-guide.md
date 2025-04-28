---
title: Security Guide
deprecated: false
hidden: true
metadata:
  robots: index
---
## Closed Virtual Network

The VerifyVASP alliance works as a Closed Virtual Network of verified members only. Only whitelisted IP addresses of Enclave servers of each VASP can securely communicate with the central server.

<br />

## Register the Enclave Server IP of VASP on VerifyVASP

* Send the IP address of the VASP enclave server to [corporate@verifyvasp.com](mailto:corporate@verifyvasp.com), the IP will be whitelisted by VerifyVASP.
* Only IP addresses are acceptable; domain addresses are not accepted.

<br />

## Register VerifyVASP Central Server IP on VASP

* You will receive a central server IP address of VerifyVASP to be whitelisted on VASP side.

<br />

## Certifying VASP API (Calling VASP API in Enclave Server)

Each VASP connecting to VerifyVASP needs to perform APIs below in its own backend server.

* API to Verify Users
  * This API returns the result after verifying that 1) the beneficiary's address and user are accurate and 2) the customer corresponding to the address have been verified in KYC and AML
* API to Verify Addresses
  * This API returns the result after discerning whether the beneficiary's wallet address entered by the originator belongs to that VASP.
* API to Return Transaction Status
  * When the beneficiary VASP requests for the transaction status of a specific verification, this API returns the transaction status as an originating VASP
* API to Decrypt DB encryption key
  * This API decrypts the "encryption key" which is used to encrypt the database
  * If the "encryption key" is entered in plain text through the environment variable, the security exposure risk increases. Therefore, this API converts "encrypted encryption key" into "decrypted encryption key."

The operations above cannot be processed within the enclave server, so the enclave delegates the operations to the API realized by each VASP. In order for the enclave server to verify API when calling VASP API, environment variables as below can be set up.

> 📘 Enclave Environment Variable:
>
> Enter VASP API verification token in `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`.
>
> Enter Header key value for VASP API verification token in `VEGA_VERIFICATION_AUTHORIZATION_KEY`.

When enclave server calls VASP APIs, those variables are passed through the HTTP header. By testing the authorization header in the VASP API to check if it is the verification token you set up, you can verify the API.

If the `VEGA_VERIFICATION_AUTHORIZATION_KEY` environment variable is not set, the authentication token is delivered through the Authorization header (bearer type) as follows.

`Authorization: Bearer <VEGA_VERIFICATION_AUTHORIZATION_TOKEN>`

If you set the `VEGA_VERIFICATION_AUTHORIZATION_KEY` environment variable, the authentication token is passed through the corresponding key value in the header. For example, if `VEGA_VERIFICATION_AUTHORIZATION_KEY=X-Api-Key` is set, the authentication token is passed as follows.

`X-Api-Key: <VEGA_VERIFICATION_AUTHORIZATION_TOKEN>`

<br />

## End-to-End Data Encryption

When a user verification request is performed through VerifyVASP, personal information of the originator and the beneficiary is sent after end-to-end encryption, so that only the VASPs can decrypt it. Thus, the VerifyVASP central server which only mediates cannot decrypt, nor does it save, user personal information.

In order to perform end-to-end encryption, the verification API of the enclave server internally operates as below.

1. In the originating VASP backend, request for beneficiary verification via the verification API of the enclave server.
2. The enclave server of the originating VASP delivers the public key request to the enclave server of the beneficiary VASP through VV central server.
3. When the enclave server of the beneficiary VASP is requested for a public key, the enclave server returns the public key saved in the DB; if necessary, the public key is created before save/return.
4. The enclave server of the originating VASP, when it receives the public key of the beneficiary VASP, encrypts personal information using the public key.
5. Through the VV central server, deliver the encrypted information to the enclave server of the beneficiary VASP.
6. The enclave server of the beneficiary VASP, which received the encrypted personal information, decrypts with a private key and verifies the user by VASP API. Then, it encrypts the personal information of the beneficiary by the public key of the originating VASP (delivered with the encrypted data).
7. Send the encrypted information to the enclave server of the originating VASP through VV central.
8. The enclave server of the originating VASP decrypts the encrypted personal information using the private key and saves it in the enclave DB.

The public key and private key used in the process of end-to-end encryption will be saved and managed in the database of the enclave server automatically. Therefore, the backend server of each VASP does not have to worry about end-to-end encryption too much. However, a few more options relevant to end-to-end encryption will be provided, so familiarization with E2E encryption might make the process more efficient.

<br />

## Options for Public key Caching

In order to increase the efficiency of the public key request process, the enclave server caches/uses the public key of the other VASP for a certain amount of time. You can determine the caching duration as an enclave environment variable.

> 📘 Enclave Environment Variable:
>
> Enter the public key caching duration in milliseconds `VEGA_PUBLIC_KEY_TTL`.
>
> The default value is 1800000 (=30 min), and the minimum value is 600000 (=10 min).

<br />

## Options for Public key Type

In order to utilize end-to-end encryption more efficiently when calling the verification API, the enclave server supports different key types as follows. The ley type can be entered in the request body when calling verification API.

* PerVasp
  * Use the key every VASP owns one. It is the most efficient by caching, but the least secure option.
* PerAddress
  * Use the key distinguished per beneficiary address. It is more secure than PerVasp key as every address is encrypted in different public keys but less efficient.
* PerVerification
  * Creates a new key per verification request. It is the most secure yet the least efficient option as caching is useless.

<br />

## Enclave Database Encryption

In the enclave database, user verification history which corresponds to virtual asset transmission is saved. Sensitive information such as the user's personal information is included in the user verification history, so such information is encrypted before saving. Moreover, end-to-end encryption is used for secure transactions of personal information, so the private keys are encrypted before they are saved in the database. The two encryption keys above - personal information encryption and private key encryption - use the same key. The encryption key can be set up as the environment variable of the enclave server.

> 📘 Enclave Environment Variable:
>
> `VEGA_ENCRYPTION_KEY_BASE64`: Set up the encryption key encoded in the base 64 formats.
>
> `VEGA_DECRYPT_API_ENDPOINT`: Set up the endpoint of API to decrypt the encryption key.

* If you do not set up the VEGA\_DECRYPT\_API\_ENDPOINT environment variable, use the value set up as the VEGA\_ENCRYPTION\_KEY\_BASE64 environment value as the encryption key in plain text. (Not recommended)
* If the VEGA\_DECRYPT\_API\_ENDPOINT environment variable is set, the value entered as VEGA\_ENCRYPTION\_KEY\_BASE64 is transferred to the request body when the corresponding API is called when the enclave is started. The value received as a response of the API is used as the encryption key of the actual DB.
  * Therefore, if you are using a service such as AWS KMS, set VEGA\_ENCRYPTION\_KEY\_BASE64 to AWS ARN and implement a server that wraps KMS in VEGA\_DECRYPT\_API\_ENDPOINT to set the endpoint, then you can extract the encryption key from KMS to ARN and use it.
* In order to decrypt the encryption key, we recommend you realize the decryption API in a separate backend server that wrapped HSM equipment.
* For decryption API specification, refer to the VASP API Reference.
* 카드 링크 추가

The list of columns that will be encrypted and saved in the database is as follows:

* `verifications table`: Table saving the user verification history
  * ivms101\_originator: Personal information of the originator
  * ivms101\_originator\_vasp: Information of the originator VASP
  * ivms101\_beneficiary: Personal information of the beneficiary
  * ivms101\_beneficiary\_vasp: Information of the beneficiary VASP
* `own_keys` table: Table saving the public key/private key used in the end-to-end encryption to verify
  * private\_key: private key using the end-to-end encryption

<br />

## Https Setting

All communications among the central server of the VerifyVASP and the enclave server of each VASP are executed via HTTPS protocol. Since the central server of the VerifyVASP directly API calls the enclave server of each VASP, each VASP needs to register the public endpoint of each server on the central server of VerifyVASP.

> 📘 Enclave Environment Variable:
>
> `VEGA_ENCLAVE_PUBLIC_ENDPOINT`: The public endpoint address of the enclave server

* The environment variable above is set up to register the public API endpoint of the enclave server to the VerifyVASP central server.
* The variable is required, and an address starting with "https\://" needs to be set up.
* Since the ways to set up https vary depending on the infrastructure environment, please refer to the appropriate method.