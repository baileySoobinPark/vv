---
title: Database Management API
api:
  file: TR_VASP_API_Spec.yaml
  operationId: travelrule-get-Decrypted-EncKey
hidden: false
---
This API is an optional implementation that allows the Enclave to dynamically retrieve a symmetric encryption key for encrypting its database.

***

<DatabaseAPI />

## Runtime Key Injection for Sensitive Data Encryption in Enclave

VerifyVASP Enclave does **not store any sensitive information in plaintext** within its database. Instead, it uses **symmetric key encryption** to secure the data. The encrypted values are stored in the database, and the same key is used to decrypt the values at runtime.

This symmetric key must be provisioned by the VASP. VerifyVASP Enclave supports the following two key injection methods:

1. Environment variable injection:

* Simple setup without API implementation
* However, the key is stored at the filesystem level, making it vulnerable to certain security threats

2. Dynamic API-based key provisioning (recommended):

* The Enclave requests the key from the VASP's backend at runtime
* The key is used in-memory only and never stored on disk
* Integrating with an external Key Management System (KMS) further enhances security
* This method is recommended when feasible for higher protection

<br />

**Option 1: Environment Variable Key Injection**\
If using this method, set the following environment variable:

* <code>VEGA\_ENCRYPTION\_KEY\_BASE64</code>: The symmetric key in base64 format. The Enclave will directly use this key for encryption and decryption.

<br />

**Option 2: API-based Key Injection (Recommended)**\
If using this method, set the following environment variables:

* <code>VEGA\_ENCRYPTION\_KEY\_BASE64</code>: A reference value (not the actual key), to be used as the <code>inputKey</code> parameter when calling your backend's decrypt API.
* <code>VEGA\_DECRYPT\_API\_ENDPOINT</code>: The endpoint of your backend decrypt API. The Enclave will send a request with the inputKey and receive the actual symmetric key in the <code>outputKey</code> field of the response.

<br />

## Implementation Guide

### Functional Requirements

#### 1. Generate and Manage Symmetric Key

* The VASP must securely generate and manage the symmetric key used for database encryption.
* You may use an internal crypto module or integrate with an external Key Management System (KMS).

#### 2. Retrieve and Return the Actual Key

* Based on the <code>inputKey</code> value provided in the request:
  * If it is an encrypted reference, decrypt it to get the key.
  * If it is a KMS key ID, query the KMS for the key.
* The actual symmetric key must be returned in the <code>outputKey</code> field of the API response.

### Recommended Practices

* Do not store plaintext keys in <code>VEGA\_ENCRYPTION\_KEY\_BASE64</code>. Instead, store only reference values and retrieve the actual key via API when needed.

* We recommend using external KMS systems such as AWS KMS \[Example]

  * `VEGA_ENCRYPTION_KEY_BASE64`: base64-encoded AES key encrypted with AWS KMS
  * `VEGA_DECRYPT_API_ENDPOINT`: endpoint of your internal API that decrypts the KMS-encrypted key

### Enclave Integration Settings

To integrate with the Enclave, set the following environment variables:

* `VEGA_DECRYPT_API_ENDPOINT`: The implementation path of your Database Management API

* `VEGA_ENCRYPTION_KEY_BASE64`: The reference value used to fetch or decrypt the actual encryption key

***

## API Specification