---
title: Callback API
api:
  file: VN_VASP_API_Spec.yaml
  operationId: verifyName-Callback
hidden: false
---
This API serves as a common interface for handling asynchronous callback events. Enclave executes this API when the Report API is invoked by the counterparty VASP.

***

## Implementation Guide

### Functional Requirements

#### 1. Branch Processing by Callback Type

Based on the `callbackType` field in the API request, you must branch the business logic according to the callback type.\
The supported callback types are as follows, and both `OWNER_VERIFICATION_RESULT_REPORT` and `OWNER_VERIFICATION_TX_REPORT` must be implemented.

<HTMLBlock>{`
<style>
  .custom-table {
    border-collapse: collapse;
    width: 100%;
    font-size: 14px;
  }

  .custom-table th,
  .custom-table td {
    border: 1px solid #ddd;
    padding: 12px;
    text-align: left;
    vertical-align: top;
  }

  .custom-table th {
    background-color: #f0f0f0;
    font-weight: 600;
  }

  .custom-table td {
    background-color: #ffffff;
  }

  .custom-table td.code-col {
    min-width: 200px;
    white-space: nowrap;
  }
</style>

<table class="custom-table">
  <thead>
    <tr>
      <th><code>callbackType</code></th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="code-col"><code>OWNER_VERIFICATION_RESULT_REPORT</code></td>
      <td>Invoked when the account ownership verification requested from the counterparty VASP has been completed and the result is delivered asynchronously.</td>
    </tr>
    <tr>
      <td class="code-col"><code>OWNER_VERIFICATION_TX_REPORT</code></td>
      <td>Used in the beneficiary VASP role. Invoked when the ordering VASP reports the transaction result.</td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

<Callback />

<br />

#### 2. Processing OWNER\_VERIFICATION\_RESULT\_REPORT Callbacks

You must perform follow-up actions based on the verification result received via the callback.

* If the final verification result is `VERIFIED`, the counterparty VASP will proceed with the asset transfer transaction.
* If the final verification result is `DENIED` or `ERROR`, the counterparty VASP has aborted the asset transfer.

Example callback messages for `OWNER_VERIFICATION_RESULT_REPORT` are provided below.

<Accordion title="Example of Callback: OWNER_VERIFICATION_RESULT_REPORT" icon="fa-info-circle">
  ```json
  // VERIFIED
  {
    "callbackType": "OWNER_VERIFICATION_RESULT_REPORT",
    "data": {
      "request_id": "64ab871b-14a3-47df-9b80-368e29fe8181",
      "verification_result": "VERIFIED",
      "result_reported_at": "2025-04-21T04:16:01.339Z"
    }
  }

  or

  // DENIED
  {
    "callbackType": "OWNER_VERIFICATION_RESULT_REPORT",
    "data": {
      "request_id": "64ab871b-14a3-47df-9b80-368e29fe8181",
      "verification_result": "DENIED",
      "reason": "MISMATCH-TICKER",
      "message": "Ticker is mismatched.",
      "result_reported_at": "2025-04-21T04:16:01.339Z"
    }
  }

  or

  // ERROR
  {
    "callbackType": "OWNER_VERIFICATION_RESULT_REPORT",
    "data": {
      "request_id": "64ab871b-14a3-47df-9b80-368e29fe8181",
      "verification_result": "ERROR",
      "reason": "UNDEFINED-ERROR",
      "message": "An internal error occurred during asset transfer.",
      "result_reported_at": "2025-04-21T04:16:01.339Z"
    }
  }
  ```
</Accordion>

<br />

#### 3. Processing OWNER\_VERIFICATION\_TX\_REPORT Callbacks

When receiving a transaction result report, you may perform the following actions:

* Verify that the reported on-chain transaction hash matches the actual deposit made to the recipient address.
* Confirm that the asset transfer request was executed and record the related information.

Example callback messages for `OWNER_VERIFICATION_TX_REPORT` are provided below.

<Accordion title="Example of Callback: OWNER_VERIFICATION_TX_REPORT" icon="fa-info-circle">
  ```json
  {
    "callbackType": "OWNER_VERIFICATION_TX_REPORT",
    "data": {
      "request_id": "64ab871b-14a3-47df-9b80-368e29fe8181",
      "tx_hash": "0xd231a7c7ff1edba061e3fbde26fe0e567fde0d2c40ff40ad1a9f3bffd999f128",
      "ordered_at": "2021-09-27T01:48:40.000Z"
      }
  }
  ```
</Accordion>

<br />

### Constraints

* This API must respond within 1 second.
* The HTTP status code in the response must be 200 OK.
* Idempotency must be guaranteed so that repeated receipt of the same callback request does not alter the processing result.
  * In other words, duplicate requests should be treated as already processed and ignored in the internal logic.
  * However, if the final verification result differs from the initial report, it must still be processed appropriately according to the updated result.

<br />

### Recommended Implementation Practices

* Since response speed is critical for the Callback API, it is recommended to process any time-consuming operations asynchronously after sending the API response.

### Enclave Integration Settings

To ensure proper integration with Enclave, configure the following environment variables:

* `VEGA_VERIFICATION_CALLBACK_API_PATH`: API path for this endpoint
* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: Authentication token for API access
* `VEGA_VERIFICATION_AUTHORIZATION_KEY`: Header key for transmitting the authentication token

***

## API Specification