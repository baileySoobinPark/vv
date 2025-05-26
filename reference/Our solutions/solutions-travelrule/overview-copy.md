---
title: Overview (COPY)
deprecated: false
hidden: true
metadata:
  robots: index
---
## Architecture

<Image align="center" border={false} caption="Diagram 1. VerifyVASP Integration Architecture Overview" src="https://files.readme.io/8d27021ec8f83d7f4cc31b17bccc04e96360c65217d142e4733739024c89930b-tr_solution_1.png" />

### VASP to VASP Communication

VASPs using TravelRule solutions for Travel Rule compliance rely on a Central Server to relay requests and responses between Originating and Beneficiary VASPs. Each VASP alternates between these roles—sending assets as an Originating VASP or receiving them as a Beneficiary VASP—depending on the transaction context.

### Enclave Integration Within VASP Infrastructure

Within each VASP infrastructure, a business backend and database operate alongside the VerifyVASP Enclave and its dedicated Enclave database. The Enclave, provided by VerifyVASP, is a prebuilt server providing protocol interface to integrate with VerifyVASP products. It must be installed and deployed within the VASP’s infrastructure.

A VASP backend can send requests to a Counterparty VASP by calling the Enclave API. A VASP receiving a request handles it within its Enclave but delegates core tasks requiring custom business logic or proprietary data to its backend through defined APIs, such as Transaction or Verification APIs. This architecture ensures a secure and modular system by establishing a loosely coupled integration between the VASP business backend and the Enclave through API calls.

### Ensuring Data Security and Privacy

The Enclave database is exclusively accessible by the Enclave, ensuring data integrity and isolation. Communication between VASPs and the Central Server is encrypted and transmitted over HTTPS channels with layered security. This protects sensitive data, including personal information, exchanged during the verification and transaction processes.

<br />

## High-level Verification Flow

1. **Originator's Asset Transfer Initiation**

   When an originator initiates a transfer request, the Originating VASP collects and prepares relevant information, including the beneficiary and identity details.
2. **Verification Request**

   The Originating VASP sends this information to the Beneficiary VASP through the VerifyVASP Central Server via **the Enclave server**, which provides the dedicated APIs.
   * The Enclave server, installed within the VASP infrastructure, enables protocol-compliant communication with the Central Server and counterparty VASPs through an end-to-end encrypted channel.
   * Developed and maintained by VerifyVASP, the Enclave is provided as a Docker image available from a private Docker Hub registry.
   * Each VASP must configure a dedicated database for the Enclave server.
   * Note that the VASP’s business backend interacts solely with the Enclave server and does not directly access the Central Server API.
3. **Verification**

   The Beneficiary VASP receives the verification request and verifies the originator's identity and beneficiary account details against its own records.
4. **Verification Result**

   After verification, the Beneficiary VASP sends the result back to the Originating VASP via VerifyVASP central server. This process includes both synchronous and asynchronous API interactions, with specific details provided in the subsequent document on scenarios and flows.
5. **Transaction Completion**

   Based on the verification result, if approved, the Originating VASP completes the withdrawal transaction on the blockchain and shares the transaction outcome with the Beneficiary VASP to confirm the transfer. The Beneficiary VASP may also query the transaction status as needed.

<br />

This process establishes a secure and structured flow of requests and responses between VASPs, ensuring efficient identity verification and transaction handling.

<br />

## Secured Data Exchange

### End-to-End Encryption for VASP Data Exchange

VerifyVASP TravelRule ensures the privacy of personal information shared during user verification by implementing robust end-to-end encryption (E2EE). This encryption guarantees that only the Originating and Beneficiary VASPs can decrypt the shared data. The VerifyVASP central server acts solely as a mediator and neither decrypts nor stores personal information.

The process of E2EE is managed seamlessly by the enclave servers of the involved VASPs, as outlined below:

1. **Initiating a Verification Request**
   * The Originating VASP's backend sends a beneficiary verification request via its enclave server's API.
2. **Public Key Retrieval**
   * The Originating VASP's enclave server requests the Beneficiary VASP's public key through the VerifyVASP central server.
   * The Beneficiary VASP's enclave server retrieves the public key from its database. If no key exists, the server generates, saves, and returns a new public key.
3. **Encrypting Personal Information**
   * Upon receiving the Beneficiary VASP's public key, the Originating VASP's enclave server encrypts the user's personal information.
   * The encrypted data is sent to the Beneficiary VASP's enclave server via the VerifyVASP central server.
4. **Decryption and Verification by Beneficiary VASP**
   * The Beneficiary VASP's enclave server decrypts the data using its private key.
   * It verifies the user information through its VASP API.
   * Next, it encrypts the Beneficiary's personal information using the Originating VASP’s public key, which is included with the encrypted data.
5. **Returning Encrypted Data**
   * The encrypted information is sent back to the Originating VASP's enclave server through the VerifyVASP central server.
6. **Final Decryption and Storage**
   * The Originating VASP's enclave server decrypts the data using its private key.
   * The decrypted information is securely stored in the enclave database.

### Key Management

The public and private keys used for encryption and decryption are automatically generated, saved, and managed within the enclave server database. This automation ensures that VASP backend systems do not need to handle E2EE directly, simplifying the implementation.

### Additional Options

While the enclave server fully manages the encryption process, optional configurations are available to enhance flexibility and control. Familiarity with E2EE concepts can further streamline and optimize this workflow.

This approach ensures secure and seamless data exchange while maintaining the privacy and integrity of sensitive user information.

<Accordion title="Configurable Key Options for End-to-End Encryption">
  ## Public Key Caching

  To improve the efficiency of public key retrieval, the enclave server caches the public key of the counterparty VASP for a configurable duration. This reduces the need for repeated requests to fetch the same key.

  * **Enclave Environment Variable**
    * Configure the caching duration in milliseconds using the variable: `VEGA_PUBLIC_KEY_TTL`.
  * **Default and Minimum Values**
    * **Default**: **`1800000`** milliseconds (30 minutes).
    * **Minimum**: **`600000`** milliseconds (10 minutes).
      Adjust this setting to balance performance and security based on your requirements.

  ## Public Key Types

  The enclave server supports multiple public key types to provide flexibility and enhanced security for end-to-end encryption during the verification process. The key type can be specified in the request body when calling the verification API. Supported keyTypes are as below.

  * `PerVasp`
    * A single key shared across all verifications for a VASP.
      * **`Pros`**: Most efficient option due to caching.
      * **`Cons`**: Least secure, as the same key is used for all verifications.
  * `PerAddress`
  * A unique key is generated for each beneficiary address.
    * **`Pros`**: More secure than PerVasp, as each address is encrypted with a unique key.
    * **`Cons`**: Slightly less efficient compared to PerVasp.
  * `PerVerification`
  * A new key is generated for every verification request.
    * **`Pros`**: Most secure, as each request uses a unique key.
    * **`Cons`**: Least efficient, as caching is not applicable.
      By selecting the appropriate caching duration and key type, you can tailor the balance between security and efficiency to meet your organization’s needs.
</Accordion>

<br />

## VASP Side Action Items for TravelRule Integration

To successfully implement TravelRule, here are the key steps VASP developers need to follow. Each step links to detailed guidance provided in subsequent sections.

1. **Review Key Scenarios and Flows**

   Gain a thorough understanding of the essential use cases and operational flows before beginning the implementation process.
2. **Define Project Scope with Future-State VASP Architecture**

   Align your implementation plan with the TravelRule architecture to ensure compatibility and scalability.
3. **Complete Onboarding**

   Register and authenticate your VASP with VerifyVASP to obtain the credentials required for integration.
4. **Develop VASP APIs**

   Implement the APIs necessary to manage transaction and verification workflows effectively.
5. **Configure a Enclave Database**

   Set up a database to store and manage verification and compliance data securely.
6. **Deploy the Enclave**

   Install and configure the Enclave Docker environment to securely connect to the VerifyVASP Central Server.
7. **Test with Robot VASP**

   Use the Robot VASP for end-to-end testing to validate your implementation and ensure protocol compliance.
8. **Plan for Ongoing Maintenance**

   Develop a strategy for maintaining the integration to accommodate regulatory updates and TravelRule enhancements.

<br />

By following these steps, you’ll be well-equipped to establish a secure and compliant integration with VerifyVASP.