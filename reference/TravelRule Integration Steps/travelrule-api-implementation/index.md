---
title: API Implementation
deprecated: false
hidden: true
metadata:
  robots: index
---
With the onboarding process complete, the next step is to implement the required REST APIs on your VASP’s business server. Before preparing for Enclave installation, these APIs must be developed to handle core actions and be callable through the Enclave.

This section outlines the APIs VASPs need to implement as part of their TravelRule integration. These APIs manage specific business logic delegated by the Enclave server, ensuring compliance with the Travel Rule and meeting jurisdictional and operational requirements. While the Enclave handles common processing logic, these APIs are customized to your VASP’s business needs and regulatory obligations.

<br />

## VASP API List to Be Implemented

The table below lists the APIs to be implemented on the VASP server, with brief descriptions. Refer to each API’s spec page for details on implementation, constraints, and specifications.

<Table>
  <thead>
    <tr>
      <th>
        API Name
      </th>

      <th>
        Provide this API as
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        Verify User Account
      </td>

      <td>
        Beneficiary VASP
      </td>

      <td>
        API to verify whether the beneficiary account address is owned by the Beneficiary VASP.
      </td>
    </tr>

    <tr>
      <td>
        Verify User
      </td>

      <td>
        Beneficiary VASP
      </td>

      <td>
        API to verify the consistency of the Beneficiary's personal information against the Beneficiary VASP owned data.

        ⚡ **A Key API for ensuring Travel Rule compliance.**
        This API provides pivotal functionality for user verification, a core requirement of the Travel Rule.

        Compared to the Verify User Account API, it entails greater complexity, particularly in validating personal information and managing intricate screening conditions. Thorough implementation and testing are crucial to effectively address business requirements and uphold compliance standards.
      </td>
    </tr>

    <tr>
      <td>
        Callback
      </td>

      <td>
        Both Originating & Beneficiary VASP
      </td>

      <td>
        API serves as a common interface for handling asynchronous flows by supporting 5 callback types listed below:

        * `VERIFICATION_RESULT`
        * `TX_REPORT`
        * `ERROR_REPORT`
        * `CHAINALYSIS_KYT_RESULT`
        * `REFINITIV_WCO_RESULT`
      </td>
    </tr>

    <tr>
      <td>
        Check Transaction Status
      </td>

      <td>
        Originating VASP
      </td>

      <td>
        API to retrieve and return the current status of a transfer transaction submitted by the Originator on blockchain, by verification identifier.
      </td>
    </tr>

    <tr>
      <td>
        Get Database Enc Key
      </td>

      <td>
        Both Originating & Beneficiary VASP
      </td>

      <td>
        API to securely get actual DB encryption key at runtime from the reference value from configuration file.
      </td>
    </tr>
  </tbody>
</Table>

<br />

## Configuring API Authentication for Enclave Calls (for VASP APIs)

To secure VASP APIs and restrict access to the Enclave server, you can configure an authentication token that is included in the HTTP headers for every API call made by the Enclave. This ensures that only authorized calls from the Enclave server are allowed.

### Setting Up Authentication Variables

The following Enclave environment variables must be configured to enable API authentication:

* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`\
  Set the value of the verification token that will be used for authentication.
* `VEGA_VERIFICATION_AUTHORIZATION_KEY`\
  Specify the header key where the verification token will be passed.

### How Authentication Works

When the Enclave server calls your VASP APIs, the configured variables are included in the HTTP headers. Your VASP server can validate the authorization header to confirm that the token matches the configured value.

* If `VEGA_VERIFICATION_AUTHORIZATION_KEY` is not set\
  The token is included in the default Authorization header as a Bearer token, formatted as follows:
  `Authorization: Bearer <VEGA_VERIFICATION_AUTHORIZATION_TOKEN>`
* If `VEGA_VERIFICATION_AUTHORIZATION_KEY` is set\
  The token is passed using the specified header key. For example, if the variable is set to `X-Api-Key`, the header will include:
  `X-Api-Key: <VEGA_VERIFICATION_AUTHORIZATION_TOKEN>`

By configuring these variables, your VASP APIs can effectively authenticate requests from the Enclave, ensuring secure and restricted access.