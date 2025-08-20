---
title: VASP API Implementation
excerpt: >-
  This is the first step for integrating with VerifyName — implementing the VASP
  API. This document outlines the specifications and requirements for the REST
  APIs that must be implemented in your VASP backend.
deprecated: false
hidden: false
metadata:
  robots: index
---
To integrate with the VerifyName protocol, all VASPs must implement the required VASP APIs — including the Originator/Beneficiary verification API — and make them accessible so that the Enclave can call them.\
This section describes the list of required APIs, their specifications, call flows, and important considerations for implementation.

<br />

## List of Required VASP APIs

Each VASP must be capable of acting as both the Ordering VASP and the Beneficiary VASP during asset transfers.\
The table below lists the essential APIs to be implemented, the role of the VASP providing each API (Ordering or Beneficiary), and the key business logic that must be performed.
For detailed implementation requirements and specifications, refer to the corresponding API Specification documents.

<HTMLBlock>{`
<style>
  .api-table {
    width: 100%;
    border-collapse: collapse;
    background-color: #fff;
    font-size: 14px;
    margin-top: 24px;
  }

  .api-table th, .api-table td {
    border: 1px solid #ddd;
    padding: 12px 14px;
    vertical-align: top;
    text-align: left;
    background-color: #fff;
    min-width: 160px;
  }

	.api-table th {
    background-color: #f8f9fa;
    color: #333;
    font-weight: bold;
  }

  .api-name a {
    color: #1364FF;
    text-decoration: none;
  }

  .api-name a:hover {
    text-decoration: underline;
  }

  .api-role {
    color: #555;
    font-weight: 500;
  }

  .callback-events code {
    background-color: #f4f4f4;
    padding: 2px 6px;
    border-radius: 4px;
    font-family: monospace;
    font-size: 13px;
    display: inline-block;
    margin: 2px 0;
  }

  .badge-key {
    display: inline-block;
    font-size: 11px;
    font-weight: 500;
    color: #fff;
    background-color: #1364FF;
    padding: 2px 6px;
    border-radius: 4px;
    margin-bottom: 6px;
  }
</style>

<table class="api-table">
  <thead>
    <tr>
      <th width=220px>API Name</th>
      <th width=175px>VASP Role</th>
      <th>API Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="api-name"><a href="#">Verify Name API</a></td>
      <td class="api-role">Ordering VASP & Beneficiary VASP</td>
      <td>Verifies the validity of the originator and beneficiary accounts, and returns the account holder’s name and date of birth to the Enclave.</td>
    </tr>
    <tr>
      <td class="api-name"><a href="#">Callback API</a></td>
      <td class="api-role">Ordering VASP & Beneficiary VASP</td>
      <td>
        A common interface that must be provided for asynchronous communication with the Enclave. The following two types of callback events can be received:
        <div class="callback-events">
          <code>OWNER_VERIFICATION_RESULT_REPORT</code>: Verification result reception<br>
          <code>OWNER_VERIFICATION_TX_REPORT</code>: Transaction result reception
        </div>
      </td>
    </tr>
    <tr>
      <td class="api-name"><a href="#">Database Management API</a></td>
      <td class="api-role">Ordering VASP & Beneficiary VASP</td>
      <td>Returns the encryption key to be used for the Enclave database and delivers it to the Runtime.</td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

<br />

## VASP API Authentication (Optional)

You can enhance security by restricting access so that VASP APIs are callable only from the Enclave.\
To do this, the VASP must:

1. Define an appropriate authentication header.
2. Implement logic to validate the authentication token passed in that header.
3. Configure Enclave environment variables so that all VASP API calls from the Enclave include the authentication header.

<br />

#### Enclave Environment Variables for Authentication

* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: The authentication token value. This value will be included as the authentication header in all requests from the Enclave.
* `VEGA_VERIFICATION_AUTHORIZATION_KEY`: The HTTP header key for passing the authentication token.
  * If not set, the default `Authorization` header with Bearer token authentication is used.
  * If set, the Enclave will include the specified header key with the token value.

**Examples**

```json
// If VEGA_VERIFICATION_AUTHORIZATION_KEY is NOT set:
Authorization: Bearer <VEGA_VERIFICATION_AUTHORIZATION_TOKEN>

// If VEGA_VERIFICATION_AUTHORIZATION_KEY를 X-Api-Key is set to X-Api-Key:
X-Api-Key: <VEGA_VERIFICATION_AUTHORIZATION_TOKEN>
```