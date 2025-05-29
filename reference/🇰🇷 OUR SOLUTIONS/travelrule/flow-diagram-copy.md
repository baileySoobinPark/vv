---
title: Flow Diagram
excerpt: >-
  Best Practice Flow를 통해 기본적인 TravelRule 트랜잭션의 흐름을 이해하고 Screening 관련 기능을 구현하기 위한
  세부 요구사항을 확인할 수 있습니다.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Best Practice

<Image align="center" border={false} caption="Sequence Diagram 1. TravelRule Best practice" src="https://files.readme.io/125494277f7e9aa4eec30651b9de394e590c20766dece1100861095183930c7f-tr_flow_diagram.png" />

The sequence diagram 1 shows the process in four key steps:\
**(1) Beneficiary VASP Selection**, **(2) Account Verification**, **(3) User Verification**, and **(4) Transaction Execution**. Each step is highlighted with a dashed box in the diagram for clarity.

<br />

### 1. Beneficiary VASP Selection

1. The originator initiates a withdrawal request from ordering VASP to the beneficiary.
2. The ordering VASP displays a screen showing a list of available beneficiary VASPs that the originator can withdraw to. This list is retrieved by calling the Get VASP List API through the Enclave.
3. The ordering VASP Enclave requests a list of VASPs from the Central Server.
4. The Central Server returns the list to the Enclave.
5. The Enclave passes the list to the VASP.
6. The ordering VASP displays the list to the user.
7. The user selects the beneficiary VASP.

<br />

### 2. Account Verification

**Information Collection**

8. To comply with the Travel Rule, the user enters the required information as prompted by the ordering VASP.
9. The ordering VASP combines the user input with internal data and calls the User Account Verification API. The request includes the beneficiary VASP ID, encryption key type, ticker, transfer information, and the beneficiary address.
10. The Enclave checks for a cached public key matching the specified keyType. If no valid key is found, the optional key exchange process (steps 11–18) is initiated.

**Key Exchange Process (Optional)**

11. The ordering VASP Enclave requests the beneficiary VASP’s public key via the Central Server.
12. The Central Server forwards the request to the beneficiary VASP Enclave.
13. The beneficiary VASP Enclave checks for a cached public key. If none is found, the Enclave generates a new key pair itself.
14. The public key is returned to the ordering VASP via the Central Server.
15. The ordering VASP Enclave receives the key.
16. If needed, the Enclave caches the key based on the specified keyType.

**Verification Request**

17. The ordering VASP Enclave encrypts sensitive user data using the beneficiary VASP’s public key.
18. It generates or retrieves a key pair to sign the request.
19. The Enclave sends the encrypted beneficiary address and related details to the beneficiary VASP via the Central Server.
20. The Central Server forwards the request to the beneficiary VASP Enclave.
21. The beneficiary VASP Enclave decrypts the request using its private key.

**Account Verification Logic**

22. The Enclave calls the Verify User Account API to check ownership of the beneficiary address.
23. The beneficiary VASP verifies the address.
24. The result is returned to the beneficiary VASP enclave
25. The enclave forwards the result to the Central Server.
26. The Central Server relays the result to the ordering VASP enclave.
27. The ordering VASP enclave passes the result to the ordering VASP.
28. If the result is DENIED, the user is notified and the withdrawal process is terminated. If VERIFIED, the ordering VASP proceeds to the next step: User Verification.

<br />

### 3. User Verification

**Verification Request**

29. Once the account is verified, the ordering VASP begins the User Verification API.
30. The enclave encrypts sensitive user data using the beneficiary VASP’s public key.
31. The encrypted requests is sent to the Central Server.
32. The Central Server queues the request and assigns a unique verification UUID, enabling asynchronous processing to accommodate potential delays on the beneficiary VASP side.
33. The Central Server returns the UUID to the ordering VASP enclave.
34. The enclave stores the UUID in its database.
35. The ordering VASP retrieves the UUID and uses it to track the verification status.
36. Simultaneously, the Central Server forwards the verification request to the beneficiary VASP.
37. The beneficiary VASP enclave decrypts the request using its private key.

**Verification Logic Delegation**

38. The beneficiary VASP enclave calls the Verify User API to perform user verification.
39. The beneficiary VASP verifies the beneficiary details. This may include optional checks such as compliance or risk screening (see Optional Screening Flow for details).
40. Once verification is complete, the beneficiary VASP returns the result, optionally including additional beneficiary information or error messages.
41. The enclave updates the request record using the UUID and encrypts the result with the ordering VASP’s public key.
42. The verification result is sent to the Central Server.
43. The Central Server notifies the ordering VASP via a Report API, delivering the result asynchronously.
44. The ordering VASP enclave decrypts the response using its private key and updates its database with the verification outcome.

**Callback and Additional Verification**

45. The ordering VASP enclave calls the Callback API, delivering the verification result and any additional beneficiary information requested.
46. The ordering VASP may optionally perform further checks based on this data, including optional screening (see Optional Screening Flow).
47. The ordering VASP must always return a 200 OK response to acknowledge receipt of the callback.

**Withdrawal Cancelation and Error Reporting**

48. If the verification result is DENIED, or if the ordering VASP decides not to proceed (e.g, due to screening failure), the user is notified, and the process is terminated.
49. If the result is VERIFIED, but the ordering VASP choose not to continue (e.g., user cancels, internal error, or high-risk result), it must notify the beneficiary VASP.
50. The ordering VASP enclave forwards the error report to the Central Server.
51. The Central Server relays it to the beneficiary VASP enclave.
52. The enclave notifies beneficiary VASP by calling the Callback API, making the verification as failed.

**Completing the Process**

53. If all verifications are successful, the ordering VASP notifies the user and proceeds to the next phase: Transaction Execution.

<br />

### 4. Transaction Execution

54. The ordering VASP creates and submits a blockchain transaction, transferring assets from the originator to beneficiary.
55. If required by the blockchain, the ordering VASP may implement finality tracking to monitor transaction status.
56. Once submitted, the ordering VASP calls the Report Transaction Result API to send the transaction hash to the beneficiary VASP. It must be done immediately after obtaining the hash.
57. The enclave maps the hash to the verification UUID and updates its database.
58. It forwards the report to the Central Server.
59. The Central Server relays the report to the beneficiary VASP enclave.
60. The beneficiary VASP enclave maps the hash to the UUID and call Callback API to share transaction details.
61. The process is complete when the beneficiary VASP returns a 200 OK response.

**Handling Exceptions: Missing Transaction Reports**

62. If the beneficiary VASP detects an on-chain deposit but hasn’t received a corresponding transaction report, call the Check Transaction Result API.
63. The request is forwarded to the Central Server.
64. The Central Server relays the request to the ordering VASP enclave.
65. The ordering VASP calls Check Transaction Result API to delegate the query.
66. The ordering VASP retrieves the transaction hash linked to the verification UUID and checks its status on-chain.
67. The result is returned through the Central Server back to the beneficiary VASP.
68. The beneficiary VASP compares the reported transaction hash with the on-chain deposit it detected.
69. If the hashes match, the beneficiary VASP updates it records accordingly.
70. The process is complete, ensuring the integrity of the matched transaction.

<br />

## Screening (Optional)

To enhance risk assessment for asset transfer transactions, VASPs can optionally integrate third-party screening services. These services enable evaluations of specific addresses, transactions, or individuals for potential risks. The flows outlined below illustrate integrations with APIs such as Chainalysis Sanction API, Chainalysis Know Your Transaction (KYT) API, and Refinitiv’s World-Check One (WCO) API.

Each API targets different aspects of risk assessment, allowing VASPs to select and utilize the service that best aligns with their compliance and operational requirements. VerifyVASP Enclave facilitates these integrations by providing an interface to request risk assessments using verification UUID. This approach streamlines the process, eliminating the need for VASPs to separately manage data for transactions that have already undergone verification.

For detailed instructions on using each API, refer to the [enclave screening API documentation](ref:travelrule-Chainalysis-Sanction) .

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