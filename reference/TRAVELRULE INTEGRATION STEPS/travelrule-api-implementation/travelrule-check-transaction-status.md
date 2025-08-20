---
title: Check Transaction Status API
api:
  file: TR_VASP_API_Spec.yaml
  operationId: travelrule-check-Transaction-Status
hidden: false
---
This API is implemented by VASPs operating in the Ordering VASP role under the TravelRule protocol. It allows the Beneficiary VASP to query the status of a transaction when an unexpected deposit is detected (e.g., due to a missing Transaction Report or prior notification).

***

## Implementation Guide

### Functional Requirements

#### 1. Persisting the <code>verificationUuid</code> and Transaction Hash (Always)

* The Ordering VASP must store the <code>verificationUuid</code> (received from the verification response) and associate it with the resulting blockchain transaction hash.
* This mapping should be reliably retrievable for future status inquiries.

#### 2. Querying and Responding with Transaction Status (When API is Called)

Upon receiving a request with a <code>verificationUuid</code>, the VASP should:

* Identify the corresponding transaction hash.
* Use the hash to query the blockchain.
* Respond with one of the following transaction statuses in the <code>transactionStatus</code> field:
  * `PENDING`: Transaction has not been submitted to the blockchain.
  * `PROCESSING`: Transaction has been submitted but not yet included in a block.
  * `WAIT-CONFIRM`: Transaction is included in a block but not yet finalized.
  * `CONFIRMED`: Transaction is mined and finality is secured.
  * `CANCELED`: Transaction was canceled before or after submission.

### Constraints

* Must respond within 1 second.

### Recommended Implementation

You can manage <code>verificationUuid</code>–transactionHash mappings without building a separate database by leveraging existing Enclave APIs:

* **Report Transaction Result API** - called immediately after broadcasting a transaction to:
  * Store the transaction hash in the Enclave database.
  * Notify the Beneficiary VASP.
* **Get Verification Result API** - use this to:
  * Retrieve the transaction hash from the Enclave using the <code>verificationUuid</code>.
  * Then check the real-time blockchain status based on the hash.

### Enclave Integration Settings

Set the following environment variables to integrate with Enclave:

* `VEGA_VERIFICATION_TRANSACTION_API_PATH`: Path to this API implementation.
* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: Token value used for authenticating Enclave requests.
* `VEGA_VERIFICATION_AUTHORIZATION_KEY`: HTTP header key for passing the token.

***

## API Specification