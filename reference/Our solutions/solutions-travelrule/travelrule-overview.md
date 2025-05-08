---
title: Overview
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

### 1. Originator's Asset Transfer Initiation

When an originator initiates a transfer request, the Originating VASP collects and prepares relevant information, including the beneficiary and identity details.

### 2. Verification Request

The Originating VASP sends this information to the Beneficiary VASP through the VerifyVASP Central Server via **the Enclave server**, which provides the dedicated APIs.

* The Enclave server, installed within the VASP infrastructure, enables protocol-compliant communication with the Central Server and counterparty VASPs through an end-to-end encrypted channel.
* Developed and maintained by VerifyVASP, the Enclave is provided as a Docker image available from a private Docker Hub registry.
* Each VASP must configure a dedicated database for the Enclave server.
* Note that the VASP’s business backend interacts solely with the Enclave server and does not directly access the Central Server API.

### 3. Verification

The Beneficiary VASP receives the verification request and verifies the originator's identity and beneficiary account details against its own records.

### 4. Verification Result

After verification, the Beneficiary VASP sends the result back to the Originating VASP via VerifyVASP central server. This process includes both synchronous and asynchronous API interactions, with specific details provided in the subsequent document on scenarios and flows.

### 5. Transaction Completion

Based on the verification result, if approved, the Originating VASP completes the withdrawal transaction on the blockchain and shares the transaction outcome with the Beneficiary VASP to confirm the transfer. The Beneficiary VASP may also query the transaction status as needed.

<br />

This process establishes a secure and structured flow of requests and responses between VASPs, ensuring efficient identity verification and transaction handling.