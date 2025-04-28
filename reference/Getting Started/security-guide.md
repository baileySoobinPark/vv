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

If you set the VEGA\_VERIFICATION\_AUTHORIZATION\_KEY environment variable, the authentication token is passed through the corresponding key value in the header. For example, if VEGA\_VERIFICATION\_AUTHORIZATION\_KEY=X-Api-Key is set, the authentication token is passed as follows.