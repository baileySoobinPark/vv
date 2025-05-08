---
title: Flow Diagram
excerpt: >-
  This page outlines the **best practice flow**, focusing on the interactions
  among the key components—Originating VASP, VerifyVASP Central Server, and
  Beneficiary VASP—during a typical transaction process.
deprecated: false
hidden: true
metadata:
  robots: index
---
## Best Practice

<Image align="center" border={false} caption="Sequence Diagram 1. TravelRule Best practice" src="https://files.readme.io/125494277f7e9aa4eec30651b9de394e590c20766dece1100861095183930c7f-tr_flow_diagram.png" />

The sequence diagram 1 illustrates the **best practice flow** for the withdrawal process using the VerifyVASP protocol. This process is divided into four key stages:\
**(1) Beneficiary VASP Selection, (2) Account Verification, (3) User Verification**, and **(4) Transaction Execution**.
Each stage is marked by a dashed box in the diagram for better segmentation.

<br />

### 1. Beneficiary VASP Selection

1. The originator user initiates a withdrawal request from an originator address (managed by the Originating VASP) to a beneficiary address (managed by the Beneficiary VASP).
2. The Originating VASP system displays a selection screen for the user to choose a Beneficiary VASP. The list of available Beneficiary VASP candidates can be generated using the response from the **Get VASP List API**.
3. Upon calling the API, the Originating VASP Enclave requests a list of VASPs from the Central Server.
4. The Central Server returns a list of currently available Beneficiary VASPs.
5. The Originating VASP Enclave forwards the list to the Originating VASP business backend.
6. The Originating VASP system displays the list of Beneficiary VASPs to the user.
7. The user selects their desired Beneficiary VASP.

<br />

### 2. Account Verification

**Information Collection**

8. To comply with the Travel Rule, the user enters the required information as prompted by the Originating VASP system.
9. The Originating VASP backend combines the user-provided data with internally managed data and initiates account verification by calling the Enclave’s **Request User Account Verification API**. The request includes the selected Beneficiary VASP ID, keyType for encryption, virtual asset information, and the beneficiary address to be verified.
10. Upon receiving the API call, the Originating VASP Enclave checks for a cached public key corresponding to the specified keyType. If no valid key is found, the optional **key exchange process** (steps 11–18) begins.

**Key Exchange Process (Optional)**

11. The Originating VASP Enclave requests a public key from the Beneficiary VASP through the Central Server.
12. The Central Server forwards this request to the Beneficiary VASP Enclave.
13. The Beneficiary VASP Enclave searches for a suitable cached public key in its key list. If none exists, it generates a new asymmetric key pair without involving the Beneficiary VASP backend.
14. The Beneficiary VASP Enclave returns the appropriate public key.
15. The public key is forwarded to the Originating VASP Enclave.
16. The Originating VASP Enclave caches the received key if necessary, based on the specified keyType.

**Verification Request**

17. Using the Beneficiary VASP's public key, the Originating VASP Enclave encrypts any sensitive personal information required for the verification request.
18. The Originating VASP Enclave generates or retrieves an appropriate key pair to sign the request.
19. The encrypted beneficiary address and other details are included in the account verification request, which the Originating VASP Enclave sends to the Beneficiary VASP via the Central Server.
20. The Central Server forwards the request to the Beneficiary VASP Enclave.
21. The Beneficiary VASP Enclave decrypts the request using its private key.

**Account Verification Logic**

22. To determine ownership of the beneficiary address, the Beneficiary VASP Enclave delegates the verification logic to the Beneficiary VASP business backend by calling the **Verify User Account API.**
23. The Beneficiary VASP backend checks the address against its database to confirm ownership.
24. The verification result is sent back to the Beneficiary VASP Enclave.
25. The result is forwarded to the Central Server.
26. The Central Server relays the result to the Originating VASP Enclave.
27. Finally, the Originating VASP Enclave passes the result to the Originating VASP backend.
28. If the verification result is **DENIED**, the Originating VASP notifies the user that the withdrawal cannot proceed, terminating the process. If the result is **VERIFIED**, the system advances to the next stage: **User Verification.**

<br />

### 3. User Verification

**Verification Request**

29. Upon successful account verification, the process advances to the user verification phase. The Originating VASP backend initiates this by calling the Request User Verification API provided by its Enclave.
30. The Originating VASP Enclave encrypts sensitive information using the Beneficiary VASP's public key.
31. The encrypted request is sent to the VerifyVASP Central Server.
32. The VerifyVASP Central Server queues the request and assigns a unique verification UUID to map the request. This asynchronous approach accommodates the potentially long processing time required for user verification on the Beneficiary VASP side.
33. The Central Server responds with the UUID to the Originating VASP Enclave.
34. The Originating VASP Enclave stores the UUID in its database.
35. The Originating VASP backend receives the UUID from the Enclave and uses it for tracking the verification request.
36. Simultaneously, the VerifyVASP Central Server forwards the verification request to the Beneficiary VASP.
37. Upon receiving the request, the Beneficiary VASP Enclave decrypts the sensitive information using its private key.

**Verification Logic Delegation**

38. The Beneficiary VASP Enclave delegates the user verification logic to its backend by calling the **Verify User API**.
39. The Beneficiary VASP backend verifies the originator and beneficiary information based on its defined policies. This process may optionally include a screening phase, such as compliance or risk assessment. Detailed steps for this are provided in the \[Optional Screening Flow] section.
40. Once verification is complete, the Beneficiary VASP backend includes additional beneficiary information (if available) in the response. For any unavailable information, an appropriate error message is returned.
41. The Beneficiary VASP Enclave updates the verification request record using the UUID and encrypts sensitive information with the Originating VASP's public key.
42. The Enclave sends the final verification result back to the VerifyVASP Central Server.
43. The Central Server notifies the Originating VASP that the verification process has been completed asynchronously. This is achieved via a new Report API call delivering the results.
44. Upon receiving the result, the Originating VASP Enclave decrypts sensitive information with its private key and updates its database with the verification outcome.

**Callback and Additional Verification**

45. The Enclave calls the **Callback API** provided by the Originating VASP backend, delivering the verification result along with any additional beneficiary information requested.
46. The Originating VASP backend may optionally perform further checks based on the additional information provided. This phase may also include a screening step as detailed in the \[Optional Screening Flow] section.
47. Since the callback request must always respond with 200 OK, the Originating VASP backend confirms receipt by returning this response to the Enclave.

**Withdrawal Cancelation and Error Reporting**

48. If the verification result from the Beneficiary VASP is **DENIED**, or if additional checks by the Originating VASP backend determine the withdrawal cannot proceed, the user is notified, and the process is terminated.
49. In cases where the Beneficiary VASP returns a **VERIFIED** result for user verification, but the Originating VASP decides not to proceed with the withdrawal (e.g., due to user cancellation, internal issues, or assessment result as high-risk transaction), it must notify the Beneficiary VASP. To avoid unnecessary waiting, the Originating VASP should inform the Beneficiary VASP that the transaction will not be completed by calling the Enclave's Report API to report an error.
50. Upon receiving the error report, the Originating VASP Enclave forwards the report to the VerifyVASP Central Server.
51. The Central Server relays the error report to the Beneficiary VASP Enclave.
52. The Beneficiary VASP Enclave informs its backend by calling the **Callback API** to finalize the failed status.

**Completing the Process**

53. If all verification steps are successfully completed, the Originating VASP communicates the results to the user and proceeds to the final phase: Transaction Execution.

<br />

### 4. Transaction Execution

54. After successfully completing the verification process, the Originating VASP initiates the blockchain transaction to transfer assets from the originator address to the beneficiary address. The Originating VASP backend creates and submits the transfer transaction.
55. For blockchains requiring finality tracking, the Originating VASP may implement additional transaction monitoring to track the status of the transaction.
56. Once the transaction is submitted, the Originating VASP backend calls the **Report Transaction Result API** of its Enclave to share the transaction hash (generated from the blockchain submission) with the Beneficiary VASP. This step must be performed immediately after obtaining the transaction hash.
57. The Originating VASP Enclave maps the transaction hash to the verification UUID and updates its database.
58. The Enclave forwards the report to the VerifyVASP Central Server.
59. The VerifyVASP Central Server relays the transaction report to the Beneficiary VASP Enclave.
60. Upon receiving the report, the Beneficiary VASP Enclave maps the transaction hash to the verification UUID in its database and calls the Beneficiary VASP backend’s **Callback API** to share the transaction details.
61. The process concludes when the Beneficiary VASP backend returns a 200 OK response to the callback request.

**Handling Exceptions: Missing Transaction Reports**

62. If the Beneficiary VASP detects an on-chain deposit to the beneficiary account but has not received the corresponding transaction report, it may initiate additional steps to verify the transaction status. To begin, the Beneficiary VASP can query the verification history for the beneficiary address to identify potential verification UUID candidates. The Beneficiary VASP calls the **Check Transaction Result API** of its Enclave for each candidate UUID.
63. The request is relayed to the VerifyVASP Central Server.
64. The Central Server forwards the request to the Originating VASP Enclave.
65. The Originating VASP Enclave delegates the query to the Originating VASP backend by calling its **Check Transaction Result API.**
66. The Originating VASP backend retrieves the transaction hash associated with the verification UUID and check on-chain processing status of the transaction.
67. The response propagates back through the Central Server to the Beneficiary VASP backend.
68. The Beneficiary VASP verifies whether the reported transaction hash matches the on-chain deposit it detected.
69. If the transaction hash matches the detected deposit, the Beneficiary VASP updates its records to reflect the transaction status.
70. The Beneficiary VASP completes the process and ensures the integrity of the matched transaction data.

<br />

## Screening (Optional)

To enhance risk assessment for asset transfer transactions, VASPs can optionally integrate third-party screening services. These services enable evaluations of specific addresses, transactions, or individuals for potential risks. The flows outlined below illustrate integrations with APIs such as Chainalysis Sanction API, Chainalysis Know Your Transaction (KYT) API, and Refinitiv’s World-Check One (WCO) API.

Each API targets different aspects of risk assessment, allowing VASPs to select and utilize the service that best aligns with their compliance and operational requirements. VerifyVASP Enclave facilitates these integrations by providing an interface to request risk assessments using verification UUID. This approach streamlines the process, eliminating the need for VASPs to separately manage data for transactions that have already undergone verification.

For detailed instructions on using each API, refer to the [enclave screening API documentation]() .

### 1. Chainalysis Sanction API Integration

<Image align="center" border={false} caption="Sequence Diagram 2. Chainalysis Sanction API integration flow for risk assessment" src="https://files.readme.io/6c2f368995602e6a646743e3e28ee61a067a9aff95941a7315a9afebe1e87947-tr_solution_2.webp" />

Sequence Diagram 2 demonstrates how both Originating VASP and Beneficiary VASP can integrate with the Chainalysis Sanction API for risk assessment. The Sanction API must be invoked after user verification request is called and is recommended to be called prior to transaction execution for pre-screening purposes. The detailed flow is as follows:

**Beneficiary VASP side risk assessment via sanction API**

1. The Beneficiary VASP may initiate a risk assessment of the originator's address by calling the Enclave API for Chainalysis Sanction API integration. The request must include the verification UUID.
2. Upon receiving the request, the Enclave generates the required requestId and the appropriate request body for the Chainalysis API call.
3. The Enclave interacts with the Chainalysis server, completing the screening process. The sanction result is retrieved and securely sent back to the Enclave.
4. The Enclave forwards the sanction result to the Beneficiary VASP’s business server for further processing.

> 📘 Note:
>
> Since this API is called after the Beneficiary VASP receives a user verification request from the Originating VASP, the Beneficiary VASP may choose to respond with a DENIED result for the related user verification if the Sanction API identifies the originator address as high-risk.

5. The Enclave stores the result in the **Sanction Results Table** within its dedicated database for future reference.

**Originating VASP side risk assessment via sanction API**

6. (\~10) The Originating VASP can also perform an optional risk assessment following the same process. The primary difference lies in the assessment target, which would be the beneficiary address in this case.

> 📘 Note:
>
> If the Sanction API identifies the Beneficiary address as high-risk, the Originating VASP may choose to cancel the asset transfer. In such cases, the Originating VASP must notify the Beneficiary VASP of the cancellation or termination by sending an ERROR REPORT.

**Transaction execution**

11. (\~15) For transactions deemed not high-risk based on the sanction results, the Originating VASP continues with the asset transfer process as outlined in the Best Practice flow. This includes executing the transaction on the blockchain and reporting the result to the Beneficiary VASP.

### 2) Chainalysis KYT API Integration

<Image align="center" border={false} caption="Sequence Diagram 3. Chainalysis KYT API integration flow for risk assessment" src="https://files.readme.io/2ac080e6cc5469ea7f1d6769eceb099cb13cb44aaed4b95becc9f69d38e42b2c-tr_solution_3.avif" />

Sequence Diagram 3 illustrates how both the Originating VASP and Beneficiary VASP can integrate with the Chainalysis KYT API for risk assessment. The KYT API supports risk assessment for both addresses and transactions.

The Originating VASP can call the KYT API to assess the risk of a beneficiary address before submitting a transaction. After creating a transaction, the Originating VASP can submit the transaction identifier to evaluate the risk associated with the transaction itself. Similarly, the Beneficiary VASP can perform transaction risk assessment by calling the KYT API after receiving a transaction result report or detecting a deposit transaction.

The detailed steps of this process are outlined below.

**Risk assessment on Beneficiary address by Originating VASP**

1. After requesting user verification from the Beneficiary VASP, the Originating VASP can initiate a risk assessment for the beneficiary address using the KYT API provided by the Enclave.
2. When the KYT API is called, the Enclave generates the required RequestId and RequestBody.
3. The Enclave sends a request to the Chainalysis server. Although depicted as a single request in the diagram, this step involves separate API calls: (1) submitting the risk assessment request and (2) retrieving the results. As such, the Enclave API operates asynchronously, returning the results when they become available.
4. The Chainalysis server evaluates the risk associated with the beneficiary address and responds with the results via the result retrieval API.
5. The Enclave stores the results in its database.
6. The Enclave calls the VASP’s Callback API to deliver the risk assessment results.

> 📘 Note:
>
> If the KYT API identifies the Beneficiary address as high-risk, the Originating VASP may choose to cancel the asset transfer. In such cases, the Originating VASP must notify the Beneficiary VASP of the cancellation or termination by sending an ERROR REPORT.

7. (\~14) For transactions that are not deemed high-risk based on the KYT results, the Originating VASP continues with the asset transfer process as outlined in the Best Practice flow. This includes executing the transaction on the blockchain and reporting the results to the Beneficiary VASP.

**Risk assessment on withdrawal transaction by Originating VASP**

15. Once the asset transfer is completed and the transaction identifier becomes available, the Originating VASP can initiate a transaction risk assessment using the KYT API via the Enclave. Before this step, the transaction result report API must have been called to provide the transaction identifier to the Enclave.
16. Upon receiving the request, the Enclave generates the required RequestId and RequestBody.
17. The Enclave sends a transaction risk assessment request to the Chainalysis server. This step also involves multiple API calls, similar to the address risk assessment process.
18. The Chainalysis server evaluates the transaction and returns the assessment results through the result retrieval API.
19. The Enclave stores the results in the relevant database table.
20. The Enclave calls the VASP’s Callback API to deliver the transaction risk assessment results, completing the process.

**Risk assessment on deposit transaction by Beneficiary VASP**

21. (\~28) This entire process can also be executed by the Beneficiary VASP after receiving a transaction report or detecting a withdrawal transaction. In such cases, the Beneficiary VASP follows the same steps to perform risk assessments for the detected transactions, enhancing the overall security and compliance of the asset transfer process.

### 3) Refinitiv WCO API Integration

<Image align="center" border={false} caption="Sequence Diagram 3. Refinitiv WCO API integration flow for risk assessment" src="https://files.readme.io/e20fb9a58375cd5403148ec1a6ea7d4f462964c57fd23f3c576893f81391fe22-tr_solution_4.webp" />

Sequence Diagram 4 illustrates how both the Originating VASP and Beneficiary VASP can integrate with the Refinitiv WCO API to conduct risk assessments. The WCO API facilitates risk evaluation of individuals (e.g., originator and beneficiary) using their personal identifiable information (PII). The detailed process is outlined below:

**Risk assessment on Beneficiary PII by Originating VASP**

1. The Originating VASP's business server calls the Enclave’s Refinitiv WCO API to initiate the risk assessment for the beneficiary’s PII.
2. Upon receiving the request, the Enclave generates the required RequestId and RequestBody for the API call.
3. The Enclave sends the risk assessment request to the Refinitiv server, including the beneficiary’s PII. While depicted as a single call in the diagram, this process may involve multiple API calls depending on the context. Consequently, the Enclave API responds asynchronously.
4. The Refinitiv server evaluates the risk and returns the results.
5. The Enclave stores the assessment results in the relevant database table.
6. (\~7) The Enclave then calls the VASP’s Callback API to deliver the results to the VASP business server.

> 📘 Note:
>
> If the WCO API identifies the Beneficiary address as high-risk, the Originating VASP may choose to cancel the asset transfer. In such cases, the Originating VASP must notify the Beneficiary VASP of the cancellation or termination by sending an ERROR REPORT.

**Risk assessment on Originator PII by Beneficiary VASP**

8. (\~14) The Beneficiary VASP can also use the WCO API to conduct risk assessments, with the key difference being the assessment target: the originator’s PII. Since the API call occurs after the Beneficiary VASP has received a user verification request, this process must be completed before returning the verification result.

<br />

**Transaction execution**

15. (\~21) For transactions that are not deemed high-risk based on the WCO results, the Originating VASP continues with the asset transfer process as outlined in the Best Practice flow. This includes executing the transaction on the blockchain and reporting the results to the Beneficiary VASP.