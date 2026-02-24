---
title: Change logs
deprecated: false
hidden: false
metadata:
  robots: index
---
# Version 2.1.1

Update: 2026-02-24

### Enclave

* Fixed an issue where a Bad MAC error intermittently occurred during E2E decryption.
  * **Upgrade Required for 2.0.0 – 2.1.0 Enclave**

<br />

# Version 2.1.0

Update: 2026-01-27

### Enclave

* Upgraded E2E encryption algorithm from CBC to GCM
* Automatically selects encryption algorithm based on counter-party's enclave version
  * GCM for 2.1.0+, CBC for older versions
  * Maintains backward compatibility with CBC-based encryption for older clients

<br />

# Version 2.0.1

Update: 2025-10-22

### Enclave

* Fixed vulnerabilities caused by outdated packages.

<br />

# Version 2.0.0

Update: 2025-08-26

* New version of docs released
  * A comprehensive guide for implementing and understanding VerifyName has been included.
  * New documentation detailing VerifyVASP integration and functionality is now available.
  * API Specification Updated: Improved readability and added detailed explanations to enhance developer understanding.

<br />

### Breaking Changes

* Added required enclave environment variable
  * **VEGA_ENCLAVE_MODE**
    * Added support for Enclave mode configuration.
    * This must be a comma-separated list containing one or more of the following: TR, VN1_CALL, VN1_RESPONSE, VN2.
* Added database table
  * **owner_verifications**
    * This table is required when one of VN1_CALL, VN1_RESPONSE, VN2 mode is enabled in VEGA_ENCLAVE_MODE.

<br />

### Major changes

* Added: New owner verification (VerifyName V2) APIs
  * [POST /v2/owner-verifications](ref:verifyname-owner-verification)
  * [POST /v2/owner-verifications/:requestId/result](verifyname-owner-verification-result-report)
  * [POST /v2/owner-verifications/:requestId/tx](verifyname-transaction-report)
  * [GET /v2/owner-verifications](verifyname-list-owner-verification)
  * [GET /v2/owner-verifications/:requestId](verifyname-get-owner-verification-result)
  * [GET /v2/vasps](verifyname-list-vasp)

<br />

* Added: New Enclave environment variables for new owner verification (VerifyName V2) VASP API
  * VEGA_VERIFICATION_VERIFY_NAME_V2_API_PATH
    * API path implemented by VASP to respond to VerifyName V2 requests.

<br />

* Added: New result code for legacy owner verification (VerifyName) API
  * MISMATCH-SYMBOL : the specified symbol is not supported by counterparty VASP

<br />

# Version 1.5.12

Update: 2026-02-24

### Enclave

* Fixed an issue where a Bad MAC error intermittently occurred during E2E decryption.
  * **Upgrade Required for 1.5.8 – 1.5.11 Enclave**

<br />

# Version 1.5.11

Update: 2025-10-22

### Enclave

* Fixed vulnerabilities caused by outdated packages.

<br />

# Version 1.5.8

Update: 2025-03-05

### Enclave

* Upgraded to Node.js version 22 for increased stability and security

<br />

# Version 1.5.7

Update: 2024-11-22

### Enclave

* (Bugfix) Fixed a hashing error during legal person verification

<br />

# Version 1.5.6

Update: 2024-09-24

### Enclave

* Fixed to support database SSL connection
  * Added environment variables for database SSL configuration in [Enclave setup](ref:travelrule-enclave-setup)  .

<br />

# Version 1.5.5

Update: 2024-07-04

### Enclave

* Fixed to not throw INVALID-FORMAT error

<br />

# Version 1.5.4

Update: 2024-01-29

### Enclave

* Added `dateOfIncorporation` field in [IVMS101 Standard](ref:ivms101-guide)  where you can fill in the date of incorporation for LegalPerson.
  * When calling the [User Verification Enclave API](ref:travelrule-encalve-request-User-Verification) , if the value `dateOfIncorporation` is present, input validation will be performed.
  * Added `LEGAL_PERSON_DATE_OF_INCORPORATION` code in [IVMS101 Personal Data Fields](ref:ivms101-guide) .
* In the geographic address data type in [IVMS101 Standard](ref:ivms101-guide) , `townName` is no longer a required field.
* (Bugfix) Fixed a bug where `buildingNumber` and `buildingName` were transposed when saving personal information with a geographic address.

<br />

# Version 1.5.3

Update: 2023-12-22

### Major changes

* To minimize wallet address or transaction ID mismatches, we provide the Wallet address and transaction ID standards.
* To improve security, the Enclave server has been modified to operate as a non-root user.
* Added the option to use a **custom schema** instead of using the public schema when using PostgreSQL.
  * Please refer to the `VEGA_DATABASE_SEARCH_PATH` environment variable in [Enclave setup](ref:travelrule-enclave-setup) .

### Enclave Bugfix

* Fixed an uncommon IVMS101 parsing error that occurred very infrequently.
* Fixed a rare occurrence of a database deadlock bug.

<br />

# Version 1.5.2

Update: 2023-09-27

### Major changes

* Added the vout field.
  * Added the `vout` field to the Request body.
    * Enclave API : [Transaction Result Report API](ref:travelrule-enclave-report-Transaction-Result)
    * VASP API : [Callback API](ref:travelrule-callback)
  * Added the `vout` field to the Response body.
    * Enclave API : [Transaction Status Query API](ref:travelrule-enclave-check-Transaction-Status) , [Get Verification Result List API](ref:travelrule-enclave-list-Verification-Result) , [Get Single Verification Result API](ref:travelrule-enclave-get-Verification-Result)
    * VASP API : [Transaction Status Query API](ref:travelrule-check-Transaction-Status)
  * **(BREAKING CHANGE)** Added the `vout` column to the `verifications` database table.
    * [verifications table scheme](ref:travelrule-database-setup)

<br />

# Version 1.5.1

Update: 2023-08-17

### Enclave Bugfix

* Fixed a bug where changing the connection state with other VASPs through the 'Vasp Connect' feature did not immediately apply the connection state.

<br />

# Version 1.5.0

Update: 2023-06-20

### Major changes

* Added APIs for Risk Assessment
  * [Risk Assessment with Chainalysis Sanction API](ref:travelrule-Chainalysis-Sanction)
  * [Risk Assessment with Chainalysis KYT API](ref:travelrule-Chainalysis-KYT)
  * [Risk Assessment with Refinitiv WCO API](ref:travelrule-Refinitiv-WCO)
* Added the `network` field.
  * Added the `network` field to the Request body.
    * Enclave API : User Account Verification API, User Verification API
    * VASP API : User Account Verification API, User Verification API
  * Added `UNKNOWN-NETWORK` to the Reason type.
    * Enclave API : User Account Verification API, Error Report API
    * VASP API : User Account Verification API, User Verification API, Callback API
  * (BREAKING CHANGE) Added the `network` column to the `verifications` database table.
    * verifications table scheme
* Added `NOT-ALLOWED` to the Reason type.
  * Enclave API : Error Report API
  * VASP API : User Verification API, Callback API

<br />

# Version 1.4.1

Update: 2022-06-15

### Enclave Bugfix

* Fixed: low probability that user verification result not stored properly in the enclave DB.

<br />

# Version 1.4.0

Update: 2022-04-22

### Major change

* Modified to allow interoperability with CODE.

### Enclave

* VASPs using CODE protocol are also displayed in the list when the list lookup API is called.
* VASP List API CountryCode and protocol items have been added in the return value..
* VASP List API Added new vaspStatus value (INTEROPERATED) in return value.

<br />

# Version 1.3.9

Update: 2022-04-15

### Enclave Bugfix

* Fixed a bug in the logic to remove information from the enclave when the user-verified VASP API returned information that was not in requiredBeneficiaryInfo .

<br />

# Version 1.3.6

Update: 2022-03-22

### Enclave Bugfix

* The verification failure issue with UNDEFINED-ERROR code on user verification VASP API request without ivms101 data fields has been resolved.

<br />

# Version 1.3.4

Update: 2022-03-17

### Enclave Bugfix

* An error occurrence from `commands` table on the Oracle database has been removed.

<br />

# Version 1.3.2

Update: 2022-03-08

### Enclave Bugfix

* The problem has been fixed that the authorization header value is misconfigured when `VEGA_VERIFICATION_AUTHORIZATION_TOKEN` variable is not configured.
* The type mismatch issue on the `count`(returned in string type) value in response payload of Verification Result List API has been fixed.

<br />

# Version 1.3.0

Update: 2022-03-80

### Major changes

* The user address verification API was expanded to a user 'account' verification API.
  * Now it supports a selective name matching verification in addition to the existing wallet address check.
  * See User Account Verification API for more details.
* Utilize a robot VASP for more test cases coverage
  * Now you can experience more various error case handling when you test the withdrawal verification with the robot VASP.
  * See Robot VASP page for more details.

### Enclave API

* API policy of Transaction Result Report API has been updated.
  * It requires to call this API as soon as the VASP finds the transaction ID (txHash) now, in contrast to the former version of the guide that required calling it after the transaction finality check.
* Error code specifications have been provided for each API.

### VASP API

* User Verification API implementation policy has been updated.
  * Only required beneficiary information fields need to be filled out.
* verificationUuid field is newly added to the request payload of User Verification API.
* Callback API implementation policy regarding the idempotent has been added.
* User Account Verification API was updated to verify the beneficiary name in case of the transaction amount exceeds the legal baseline.

### Enclave

* The name of an environmental variable has been changed as `User Address Verification API` is changed to `User Account Verification API`.
  * AS-IS: `VEGA_VERIFICATION_ADDRESS_API_PATH`
  * TO-BE: `VEGA_VERIFICATION_ACCOUNT_API_PATH`
* A new environment variable for VASP API Authorization Token header setup has been added.
  * See Enclave Install and Security Guide pages for more details.
* The index definition of commands table updated.
  * A new column `created_at` is added to the index.
  * See Database page for more details.

<br />

# Version 1.2.0

Update: 2022-02-23

### Major changes

* Some enclave APIs were changed to work asynchronously.
  * Data that was delivered with synchronous API response is now delivered through the callback VASP API.
  * <Accordion title="Why did VerifyVASP introduce asynchronous processes?">
      The user verification process necessarily includes connections to other VASPs whose immediate completion is not able to be guaranteed.

      In this situation, synchronous interlocking might result in the burden of staying connected until the response comes, which can be a bottleneck at the time of heavy requests.

      Therefore, some APIs are now changed to work in a non-blocking way. Get the processing result through the newly added callback API from now on.
    </Accordion>
* Specific personal data specification requirements now can be designated in the request phase to VASP.
  * Refer to the newly added code specification to designate the data requirements.
  * <Accordion title="Background of using personal data field code">
      When sharing user personal information between VASPs, the required fields might vary along with the policy of each VASP or national regulation.

      For example, a VASP may need an additional field such as the date of birth for sanction screening, or geographical address to meet the legal necessities.

      Simply including the additionally required data fields upon the request or response enables the VASPs to collect required data without any unnecessary custom precessing. All the verification requester VASPs must fill the requested fields to satisfy the data request, or the verification should be DENIED.
    </Accordion>
* VASP API implementation policy updated.
* <Accordion title="VASP API Policies">
    VerifyVASP, a Travel Rule solution, is a collaborative protocol among multiple VASPs.

    Each VASP has its own internal operating policy or different implementation standard, which makes it hard to orchestrate the system to work fine.

    Accordingly, building a minimal consensus on the implementation policy can help the counter-side VASP reduce unexceptional errors and build the system easily.
  </Accordion>
* Now VerifyVASP supports a transaction in a single VASP, meaning a VASP can be both the originating VASP and beneficiary VASP of the transaction.
* <Accordion title="Supporting transaction inside a VASP">
    It is not mandatorily required for the users who trade virtual assets in the same VASP to be verified via VerifyVASP.

    However, the VASP might need to operate an additional system to track the verification history of internal transactions under the regulation. Integrating VerifyVASP for both internal and external transactions is recommended to reduce the operational overhead.
  </Accordion>

### Enclave API

* The Asynchronous process is introduced to these APIs below.
  * User Verification API
  * Transaction Result Report API
  * Error Report
* User Verification API spec updated.
  * requiredBeneficiaryInfo field was added to the request payload.
  * Due to the change to work asynchronously, some fields from the response payload were discarded including verificationUuid.
  * The discarded data fields are now delivered through the Callback VASP API.
* Error report API specification updated.
  * message field was added to the request payload.
* New APIs added
  * An API to check the VASP ID assigned to itself
  * An API for verification result of single request history by UUID

### VASP API

* Callback API is newly added.
  * The API is for getting notifications of the processing result asynchronously.
  * Strongly recommended implementing the callback API: Polling way of implementation for all the processing transaction results without enough care might be a burden to both VASP itself and the VerifyVASP network.
  * Implementation policies for each VASP API were updated.
  * Error codes from the user verification API are now provided.

### Enclave

* The database table definition was updated.
  * Added: commands table
  * Modified: verifications table
    * message column is added
* An environmental variable was added
  * `VEGA_VERIFICATION_CALLBACK_API_PATH`: A variable for callback API endpoint setup

### IVMS101

* IVMS101 Message Format Guide was updated.
  * Address field expansion to support multiple addresses in addition to the parent address (e.g. XPR, EOS)
  * The geographic address policy now requires corporations to fill out both headquarters address and branches.

### Bugfix and minor updates

* The problem in setting database port number adjustment has been fixed.
* The endpoint scanning process during enclave boot now checks the endpoints of transaction status query API and address verification API too.
* A diagram describing the transaction state transition has been added to the transaction status query API page.
* Parameter names have been changed.
  * The query parameters for Verification Result List API are modified as follows.
    * fromAccount --> originatorAccountNumber
    * toAccount --> beneficiaryAccountNumber

<br />

# Version 1.1.0

Update: 2022-02-08

### Consol Site

* Korean Site is open.
  * Only the staging environment and site are open; the production environment and site are to open soon.
  * The address of the Korean VerifyVASP console site updated ([https://kr.verifyvasp.xyz](https://kr.verifyvasp.xyz)).
  * Korean VASPs need to register to the Korean console site newly.

### Central Server

* The central server endpoint for the Korea region was added.

### Enclave Server

* Enclave server - version updated (VerifyVASP/enclave:v1.1.0)
* Enclave database - table definition updated.
  * verifications
* Enclave database - new tables
  * counter_party_keys
  * own_keys
* Enclave database applies encryption to some columns
* Environment variables added
  * `VEGA_ENCLAVE_PUBLIC_ENDPOINT`
  * `VEGA_VERIFICATION_ADDRESSES_API_PATH`
  * `VEGA_VERIFICATION_TRANSACTION_API_PATH`
  * `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`
  * `VEGA_ENCRYPTION_KEY_BASE64`
  * `VEGA_DECRYPT_API_ENDPOINT`
  * `VEGA_PUBLIC_KEY_TTL`
  * `VEGA_LOG_LEVEL`

### VASP API

* VASP side need-to-implement APIs were added
  * User address verification API (mandatory)
  * Transaction status query API (mandatory)
  * Decrypting database encryption key API (recommended)
* Specification update for user verification API
  * symbol, amount fields moved into assetInfo object

### Enclave API

* API version information was added to all API paths.
* Updated APIs
  * VASP List API
    * The list is changed to include VerifyVASP members only by default.
    * includesAll query parameter is now supported.
    * The response provides more detailed information including VASP health status.
  * User verification API
    * request body modified
  * New APIs are now supported.
    * Address verification API
    * Error report API
    * Transaction status query API

### Minor Updates

* A security guide page was added.
* IVMS101 standard guiding page was added.
* IVMS101 message format guide page was added.
