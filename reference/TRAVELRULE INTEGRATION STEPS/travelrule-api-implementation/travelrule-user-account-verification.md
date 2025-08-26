---
title: Verify User Account API
api:
  file: TR_VASP_API_Spec.yaml
  operationId: travelrule-User-Account-Verification
hidden: false
---
The Verify User Account API is called when a VASP is acting as the **Beneficiary VASP** in the TravelRule protocol. It verifies whether the beneficiary wallet address provided by the originator belongs to the Beneficiary VASP and returns the result.

***

## Implementation Guide

### Functional Requirements

#### 1. Verify VASP Address Ownership

* Check whether the <code>beneficiary.accountNumber</code> in the API request exists in your VASP’s issued addresses.
* Compare the value against your database and return the result.

#### 2. Return Verification Result

* Return the result in the <code>accountVerificationResult</code> field.
  * If the address belongs to your VASP → <code>VERIFIED</code>
* If the address does not belong to your VASP → <code>DENIED</code>
  * When returning <code>DENIED</code>, you must include a <code>reason</code> field with one of the following codes:

<HTMLBlock>{`
<HTMLBlock>
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
    min-width: 250px;
    white-space: nowrap;
  }
</style>

<table class="custom-table">
  <thead>
    <tr>
      <th>Code</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="code-col"><code>UNKNOWN-SYMBOL</code></td>
      <td>Unsupported virtual asset symbol (e.g., asset not listed by your exchange)</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNKNOWN-NETWORK</code></td>
      <td>Unsupported network (e.g., USDT-Ethereum requested but only USDT-Tron supported)</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNKNOWN-ADDRESS</code></td>
      <td>Address not recognized</td>
    </tr>
    <tr>
      <td class="code-col"><code>MISMATCHED-NAME</code></td>
      <td>Beneficiary name does not match</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNVERIFIED-KYC</code></td>
      <td>KYC process not completed</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNDEFINED-ERROR</code></td>
      <td>Undefined or other error</td>
    </tr>
  </tbody>
</table>
</HTMLBlock>
`}</HTMLBlock>

<br />

### Constraints

* The API must return a response within 1 second.

### Recommended Implementation

* This API should only verify address ownership.
* It is recommended not to include KYC, sanction screening, or other risk evaluations in this endpoint.

### Environment Variable Configuration

Set the following Enclave environment variables for integration:

* `VEGA_VERIFICATION_ACCOUNT_API_PATH`: Path to this API endpoint
* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: Authentication token value for API access
* `VEGA_VERIFICATION_AUTHORIZATION_KEY`: HTTP header key for passing the authentication token

### Related Links

* [IVMS101 Guide](ref:ivms101-guide)

***

## API Specification
