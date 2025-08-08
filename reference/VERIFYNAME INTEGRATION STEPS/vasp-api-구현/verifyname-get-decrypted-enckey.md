---
title: Database Management API
api:
  file: VN_VASP_API_Spec.yaml
  operationId: verifyName-get-Decrypted-EncKey
hidden: false
---
This API is an optional implementation requirement used to dynamically deliver the symmetric key for Enclave database encryption.

***

<DatabaseAPI />

## Symmetric Key Injection for Enclave Sensitive Data Encryption

VerifyVASP Enclave does not store sensitive data in plain text in its database. Instead, it protects data through symmetric key–based encryption. Data is stored in encrypted form and decrypted upon retrieval.

The symmetric key used for encryption and decryption must be generated and injected by the VASP. VerifyVASP Enclave supports two key injection methods:

1. Environment Variable Injection – Directly configure the symmetric key in an environment variable.
2. API-based Injection (this API) – Implement this API to allow Enclave to retrieve the symmetric key at runtime.

<br />

**Method 1: Environment Variable Injection**

* Advantage: Easy to set up without API implementation.
* Disadvantage: The key is stored at the file system level, which is vulnerable to security threats.

If using this method:

* Set the symmetric key value directly in the `VEGA_ENCRYPTION_KEY_BASE64` environment variable.
* Enclave will use this value directly for encryption and decryption.

<br />

**Method 2: API-based Injection (Recommended)**

* Advantage: The key is loaded into memory at runtime, without persistent storage, improving security.
* Allows integration with external Key Management Systems (KMS) for enhanced key protection.

If using this method:

* Set `VEGA_ENCRYPTION_KEY_BASE64` to a reference value (used in the `inputKey` field of the request).
* Set `VEGA_DECRYPT_API_ENDPOINT` to the endpoint path of this API implementation.
* Enclave will call this API using the configured reference value, and use the `outputKey` from the API response for encryption and decryption.

<br />

## Implementation Guide

### Functional requirements

#### 1. Symmetric Key Generation & Management

* The VASP must establish a secure process for generating and managing symmetric keys for database encryption.
* Keys can be generated using an internal encryption module or obtained from an external security system such as a Key Management System (KMS).

#### 2. Key Retrieval & Return

* Use the `inputKey` reference value provided in the request to retrieve the actual symmetric key.
  * If the reference value is an encrypted key, decrypt it.
  * If the reference value is a KMS key ID, retrieve the key from the KMS.
* Include the retrieved key in the `outputKey` field of the API response.

### Recommended Practices

* Do not store the symmetric key in plain text in the `VEGA_ENCRYPTION_KEY_BASE64` environment variable. Instead, store only the reference value.

* Use an external KMS (e.g., AWS KMS) for better key protection.

  \[Example]

  * `VEGA_ENCRYPTION_KEY_BASE64`: Symmetric key encrypted with AWS KMS
  * `VEGA_DECRYPT_API_ENDPOINT`: Internal API endpoint that performs AWS KMS decryption

### Enclave Integration Settings

To ensure proper integration with Enclave, configure the following environment variables:

* `VEGA_DECRYPT_API_ENDPOINT`: API path for this endpoint

* `VEGA_ENCRYPTION_KEY_BASE64`: Reference value for the symmetric key

***

## API Specification