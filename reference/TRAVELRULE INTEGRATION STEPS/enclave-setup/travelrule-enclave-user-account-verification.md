---
title: Request User Account Verification API
api:
  file: TR_Enclave_API_Spec.yaml
  operationId: travelrule-Enclave-User-Account-Verification
hidden: false
---
This API is used by the sending VASP to request ownership verification of a specific wallet address from the receiving VASP. The response will confirm whether the provided address is owned by the receiving VASP.

***

## Verification Result

The verification result can be found in the `accountVerificationResult` field of the response. If the result is `DENIED`, the reason field will contain one of the following failure reasons.

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
    min-width: 250px;
    white-space: nowrap;
  }
</style>

<table class="custom-table">
  <thead>
    <tr>
      <th>Reason Code</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="code-col"><code>UNKNOWN-SYMBOL</code></td>
      <td>Unsupported virtual asset symbol (e.g., symbol not supported on the exchange)</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNKNOWN-NETWORK</code></td>
      <td>Unsupported network (e.g., USDT-Ethereum requested but only USDT-Tron supported)</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNKNOWN-ADDRESS</code></td>
      <td>Unrecognized or unidentifiable address</td>
    </tr>
    <tr>
      <td class="code-col"><code>MISMATCHED-NAME</code></td>
      <td>Beneficiary name mismatch</td>
    </tr>
    <tr>
      <td class="code-col"><code>BLACKLISTED</code></td>
      <td>Beneficiary wallet address is on a sanctions or blacklist</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNVERIFIED-KYC</code></td>
      <td>User has not completed KYC verification</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNDEFINED-ERROR</code></td>
      <td>Undefined or unknown error</td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

***

## API Specification