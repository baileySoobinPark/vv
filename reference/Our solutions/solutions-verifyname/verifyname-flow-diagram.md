---
title: Flow Diagram
excerpt: >-
  This page outlines the VerifyName best practice flow, focusing on the
  interactions among the key components—Originating VASP, VerifyVASP Central
  Server, and Beneficiary VASP—during a typical transaction process.
deprecated: false
hidden: true
metadata:
  robots: index
---
In the sections that follow, you will discover:

* A high-level overview of critical steps and their objectives.
* A detailed breakdown of each interaction depicted in the sequence diagram.
* Essential considerations and recommendations to ensure security and regulatory compliance throughout the workflow.

## Post-Verification - From Non-Obliged VASP to Travel Rule Obliged VASP

Sequence Diagram 1 illustrates the post-verification flow, detailing how ownership verification between Originator and Beneficiary accounts is conducted after a blockchain transaction is confirmed. This scenario occurs when the unregulated Originating VASP skips the verification process, prompting the regulated Beneficiary VASP to initiate a post-verification request to ensure compliance.

<Image align="center" border={false} caption="Sequence Diagram 1. VerifyName integration flow for unregulated VASP originating withdrawal" src="https://files.readme.io/7e75c4995f2b8b686ba210d9793debccd5c7b8a14dd71545b324fff0d665092b-Post_Verification.svg" />

1. The Originator user requests a withdrawal from his/her account with the Originating VASP.
2. As the Originating VASP is unregulated, it directly executes the transaction on the blockchain without pre-verification, sending assets to the Beneficiary address.
3. Once the transaction is mined on the blockchain,
4. The Beneficiary VASP detects the deposit to the Beneficiary address.
5. To identify the origin, the Beneficiary VASP may prompt the user to specify the Originating VASP. To facilitate this, the Beneficiary VASP can use the Enclave's List Provider API, which retrieves a list of VASPs capable of performing ownership verification (referred to as providers). The Enclave queries the VerifyVASP Central Server in real-time to obtain this provider list.
6. The Central Server returns a list of provider VASPs to the BV.
7. Based on this list, the Beneficiary VASP prompts the user to select the Originating VASP responsible for the withdrawal.
8. After the user provides the information:
9. The Beneficiary VASP server includes the selected provider VASP identifier and calls the Enclave's **Owner Verification API**, passing details such as the Beneficiary account owner’s name, date of birth, and the detected transaction hash.
10. The Beneficiary VASP side Enclave hashes the name and date of birth.
11. It forwards the hashed values to the VerifyVASP Central Server, which then routes the verification request to the selected Originating VASP.
12. The Central Server calls the **VerifyName API** of the selected provider VASP.
13. Upon receiving the request, the Originating VASP server identifies the transaction origin address by referencing the transaction hash.
14. The Originating VASP server retrieves the name and date of birth of the Originator from its database.
15. Following the VerifyName guidelines, the Originating VASP server hashes these values.
16. By comparing the generated hash with the one included in the request, the Originating VASP confirms whether the Originator and Beneficiary accounts belong to the same owner.
17. The verification result is returned to the VerifyVASP Central Server.
18. The Central Server relays the result back to the Beneficiary VASP Enclave.
19. The Beneficiary VASP Enclave then sends back the result to the Beneficiary VASP business server.
20. The Beneficiary VASP server updates the deposit status to indicate the completion of the ownership verification.
21. The Beneficiary VASP may notify the Originator user of the successful deposit verification.

<br />

## Pre-Verification - From Travel Rule Obliged VASP to Travel Rule Non-Obliged VASP

In contrast to the post-verification case, when the Originating VASP is a regulated entity, it performs account verification before executing the transfer. If the counterparty VASP is a provider VASP supporting the verifyName API, the Originating VASP can leverage the VerifyVASP Central Server to call this API and complete the pre-verification process.

<Image align="center" border={false} caption="Sequence Diagram 2. VerifyName integration flow for regulated VASP originating withdrawal" src="https://files.readme.io/80710acf01186f91afcf4c743fe5f62d3256dca8936b36b56dc603734993d3fe-Pre_Verification.svg" />

1. The originator initiates a withdrawal from the Originating VASP side account. During this process, the user must designate a Beneficiary VASP through a series of actions facilitated by the Originating VASP’s interface.
2. The Originating VASP calls the Enclave's Request Owner Verification API to initiate pre-verification for the beneficiary account. Since the transaction has not yet occurred, the request includes the beneficiary account address instead of a transaction hash.
3. The Beneficiary VASP's Enclave hashes the provided name and date of birth information according to the VerifyName guidelines.
4. The hashed data is included in a verification request sent to the VerifyVASP Central Server.
5. The Central Server forwards the request to the Beneficiary VASP's VerifyName API, which must be implemented by the Beneficiary VASP.
6. The Beneficiary VASP queries its database to retrieve the user information associated with the specified beneficiary account address.
7. It retrieves the user's name and date of birth.
8. The Beneficiary VASP hashes these values following the VerifyName guidelines to produce a hash value.
9. It then compares the generated hash with the hash included in the request. This confirms whether the name and date of birth of the Originator user and Beneficiary user match.
10. The verification result is returned to the VerifyVASP Central Server.
11. And the VerifyVASP Central Server relays the result back to the Originating VASP's Enclave.
12. Ultimately the Originating VASP’s business server can get response. All actions between 2 to 12 are performed synchronously.
13. If the verification result is `VERIFIED`, the Originating VASP submits the transfer transaction to the blockchain for execution.

14-15. Once the transaction is propagated and mined, the Originating VASP confirms the result.

16. Based on the service flow, the Originating VASP notifies the user that the withdrawal is complete, marking the end of the withdrawal process.

<br />

<Image align="center" src="https://files.readme.io/708fc2ab008928fdc52752c6957aecce67799e8c84698b37ce87a088e8dbb47a-sequence_diagram.drawio_2.png" />