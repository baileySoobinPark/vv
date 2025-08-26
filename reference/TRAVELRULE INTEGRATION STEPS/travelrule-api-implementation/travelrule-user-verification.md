---
title: Verify User API
api:
  file: TR_VASP_API_Spec.yaml
  operationId: travelrule-User-Verification
hidden: false
---
The **Verify User API** is called when a VASP acts as the **Beneficiary VASP** in the TravelRule protocol. It is invoked by the Beneficiary VASP’s Enclave upon receiving a request from the Ordering VASP.
The Beneficiary VASP uses this API to verify the beneficiary’s information, confirm compliance with regulatory requirements, and determine whether to allow the asset transfer.

***

## Implementation Guide

### Functional Requirements

#### 1. Verify Personal Information

* Compare the beneficiary’s personal information, provided in IVMS101 format, against your VASP’s records.
* This includes name verification and address verification.

#### 2. Verify Travel Rule Regulatory Compliance

Check whether the transaction meets compliance requirements by verifying:

* KYC completion status
* AML policy compliance
* STR monitoring and sanction screening for the originator
* Any additional filtering as required by your VASP’s internal policies

#### 3. Return Requested Information from Ordering VASP

If the Ordering VASP specifies <code>requiredBeneficiaryInfo</code>, the requested fields must be returned in the <code>>ivms101</code> object.

* If the requested information is unavailable or cannot be provided:
  * Set verificationResult to <code>DENIED</code>
  * Set reason to <code>UNAVAILABLE-INFORMATION</code>
* Only return requested fields; unrequested fields must be returned as empty values.
* Return the wallet address exactly as provided in the request.
  * If the address is invalid, set <code>verificationResult</code> to <code>DENIED</code>.

#### 4. Return Verification Result

Return the final result in the <code>result</code> field:

* <code>VERIFIED</code> → No issues found; include beneficiary details in IVMS101 format.
* <code>DENIED</code> → Issues found; include one of the following failure reason codes in both <code>reason</code> and <code>message</code> fields:

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
      <th><code>Reason Code</code></th>
      <th><code>Message</code></th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="code-col"><code>UNKNOWN-SYMBOL</code></td>
      <td>Unsupported asset symbol<br>(e.g., "ETH")</td>
      <td>A virtual asset not supported by the VASP (e.g., an asset not listed on the exchange)</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNKNOWN-NETWORK</code></td>
      <td>Unsupported network name<br>(e.g., "Ethereum")</td>
      <td>A network not supported by the VASP (e.g., USDT-Ethereum requested but only USDT-Tron supported)</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNKNOWN-ADDRESS</code></td>
      <td>Target address<br>(e.g., "0xasd...")</td>
      <td>Unrecognized wallet address</td>
    </tr>
    <tr>
      <td class="code-col"><code>LACK-OF-INFORMATION</code></td>
      <td>Missing fields separated by commas<br>(e.g., "ACCOUNT_NUMBER")</td>
      <td>Insufficient originator information</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNAVAILABLE-INFORMATION</code></td>
      <td>Unavailable fields separated by commas<br>(e.g., "ACCOUNT_NUMBER")</td>
      <td>Beneficiary information unavailable</td>
    </tr>
    <tr>
      <td class="code-col"><code>BLACKLISTED</code></td>
      <td>Target address<br>(e.g., "0xasd...")</td>
      <td>Address is on a sanctions list</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNVERIFIED-KYC</code></td>
      <td>-</td>
      <td>KYC not completed</td>
    </tr>
    <tr>
      <td class="code-col"><code>MISMATCHED-NAME</code></td>
      <td>-</td>
      <td>Beneficiary name mismatch</td>
    </tr>
    <tr>
      <td class="code-col"><code>NOT-ALLOWED</code></td>
      <td>Reason<br>(e.g., "This user is locked by internal policy.")</td>
      <td>Blocked due to internal policy</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNDEFINED-ERROR</code></td>
      <td>-</td>
      <td>Undefined or other error</td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

### Constraints

* The API must respond within 5 seconds.

### Recommended Implementation

* Apply the Travel Rule protocol to all asset transfers, regardless of regulatory obligation, to strengthen security and compliance while reducing operational risk.
* If the Ordering VASP’s request includes <code>isExceedingThreshold</code>: <code>false</code>, the transaction is not subject to the Travel Rule and may be processed without user verification.

### Enclave Integration Settings

Set the following Enclave environment variables for integration:

* `VEGA_VERIFICATION_API_PATH`: API endpoint path
* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: API authentication token value
* `VEGA_VERIFICATION_AUTHORIZATION_KEY`: HTTP header key for passing the authentication token

### Related Links

* [IVMS101 Guide](ref:ivms101-guide)

***

## API Specification
