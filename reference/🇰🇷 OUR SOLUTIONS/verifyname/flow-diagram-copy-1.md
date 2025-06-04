---
title: Scenarios and Flows
excerpt: >-
  본 문서에서는 VerifyName 2.0 프로토콜을 사용한 사후 검증(Post-Verification) 및 사전
  검증(Pre-Verification) 시나리오와 Flow를 소개합니다. 각 VASP는 상대 VASP의 요건에 따라 적절한 시나리오를 선택하여
  수행할 수 있습니다.
deprecated: false
hidden: false
metadata:
  robots: index
---
In the sections that follow, you will discover:

* A high-level overview of critical steps and their objectives.
* A detailed breakdown of each interaction depicted in the sequence diagram.
* Essential considerations and recommendations to ensure security and regulatory compliance throughout the workflow.

## Post-Verification - From Non-Obliged VASP to Travel Rule Obliged VASP

Sequence Diagram 1 illustrates the post-verification flow, detailing how ownership verification between Originator and Beneficiary accounts is conducted after a blockchain transaction is confirmed. This scenario occurs when the unregulated ordering VASP skips the verification process, prompting the regulated Beneficiary VASP to initiate a post-verification request to ensure compliance.

<Image align="center" border={false} caption="Sequence Diagram 1. VerifyName integration flow for unregulated VASP originating withdrawal" src="https://files.readme.io/7e75c4995f2b8b686ba210d9793debccd5c7b8a14dd71545b324fff0d665092b-Post_Verification.svg" />

1. The originator initiates a withdrawal request from ordering VASP to the beneficiary.
2. As the Ordering VASP is unregulated, it executes the transaction on the blockchain without pre-verification, sending assets to the Beneficiary address.
3. Once the transaction is mined on the blockchain, the Beneficiary VASP detects the deposit to the Beneficiary address.
4. To identify the Originator, the Beneficiary VASP calls the VASP LIST API to retrieve the list of the VASPs that can deposit to.
5. The Enclave requests a list of VASPs from the Central Server.
6. The Central Server returns a list of VASPs.
7. The Enclave returns a list of VASPs.
8. The Beneficiary VASP displays the list to the user.
9. The Beneficiary selects the Ordering VASP.
10. The Beneficiary VASP calls the Owner Verification API to verify whether the Originator and the Beneficiary are the same entity. The request includes the digital asset information and the Beneficiary’s name and date of birth.
11. The Beneficiary VASP's Enclave generates Salt.
12. The generated salt is used to hash the Beneficiary's name and date of birth.
13. The salt is encrypted using the Ordering VASP’s public key.

14 \~ 15. The Enclave passes the required Owner Verification data, including the hashed name and date of birth and encrypted salt to the Ordering VASP’s Enclave.

16. The Ordering VASP’s Enclave initiates the verification process by calling the Ordering VASP’s VerifyName API. Only digital asset information—such as the network, ticker, and transaction hash—is provided to the Ordering VASP.
17. The Ordering VASP server verifies whether the received information matches the details of the transaction it has sent.
18. If a matching value is found, the Originator is identified using the transaction hash, and their name and date of birth are retrieved from the database.
19. The Ordering VASP returns the verification result of the digital asset transfer, along with the Originator’s name and date of birth, to the Ordering VASP Enclave.
20. The Enclave decrypts the encrypted salt using the Ordering VASP's private key.
21. The Originator’s name and date of birth are hashed using the salt.
22. The hashed name and date of birth are compared with the hashed name and date of birth received from the Beneficiary VASP.

23 \~ 25. Returns the digital asset verification result and the hash comparison result.

26 \~ 27. Based on the returned verification data, the Beneficiary VASP determines the final result and submits a report to share its decision.

28 \~ 30. The reported final verification result is shared with the Ordering VASP via the Callback API.

31 \~ 32. **\[optional]** Based on the final verification result, both the Ordering VASP and the Beneficiary VASP can process deposits and withdrawals, and notify the user accordingly.

<br />

## Pre-Verification - From Travel Rule Obliged VASP to Travel Rule Non-Obliged VASP

In contrast to the post-verification case, when the Ordering VASP is a regulated entity, it performs owner verification before executing the transfer.

<Image align="center" border={false} caption="The Beneficiary VASP can confirm the deposit and notify the user based on the transaction status." src="https://files.readme.io/0446271b8557cb4007d6f656d8972440b023a65d5ea6871e346744775979198b-Pre_Verification.svg" />

1. The originator initiates a withdrawal request from ordering VASP to the beneficiary.
2. The Ordering VASP calls the Owner Verification API to verify whether the Originator and the Beneficiary are the same entity. the request includes digital asset information, along with the Beneficiary’s name, date of birth, and address.
3. The Ordering VASP's Enclave generates Salt.
4. The generated salt is used to hash the Originator's name and date of birth.
5. The salt is encrypted using the Beneficiary VASP’s public key.

6 \~ 7. The Enclave passes the required Owner Verification data, including the hashed name and date of birth and encrypted salt to the Beneficiary VASP’s Enclave.

8. The Beneficiary VASP’s Enclave initiates the verification process by calling the Beneficiary VASP’s VerifyName API. Only digital asset information—such as the network, ticker, and Beneficiary address —is provided to the Beneficiary VASP.
9. The Beneficiary VASP server verifies whether the received digital asset information corresponds to an asset managed by the Beneficiary VASP.
10. If a matching value is found, the Beneficiary is identified using the Beneficiary address, and their name and date of birth are retrieved from the database.
11. The Beneficiary VASP returns the verification result of the digital asset, along with the Beneficiary name and date of birth, to the Beneficiary VASP Enclave.
12. The Enclave decrypts the encrypted salt using the Beneficiary VASP's private key.
13. The Beneficiary's name and date of birth are hashed using the salt.
14. The hashed name and date of birth are compared with the hashed name and date of birth received from the Ordering VASP.

15 \~ 17. Returns the digital asset verification result and the hash comparison result.

18 \~19. Based on the returned verification data, the Ordering VASP determines the final result and submits a report to share its decision.

20 \~ 22. The reported final verification result is shared with the Ordering VASP via the Callback API.

23. **\[optional]** If the Ordering VASP reports the final verification result as a failure, it may notify the Originator of the withdrawal cancellation along with the reason for the cancellation.
24. If the Ordering VASP reports the final verification result as successful, the digital asset transfer transaction is executed.

**Steps 25 to 30 describe the case in which the transaction execution is reported after the transaction has been performed.**

25. After executing the transaction, the transaction hash is returned.
26. The Owner Verification Transaction Result API is called using the request ID and transaction hash returned from the Owner Verification result.

27 \~ 30. The Beneficiary VASP can check the transaction execution report result via the Callback API and, based on the result, confirm the deposit and notify the Beneficiary.

**Steps 31 to 39 describe the flow in which the Beneficiary VASP checks the transaction status when the Ordering VASP has not reported the transaction execution after performing the transaction.**

31. The Beneficiary VASP calls the Check Transaction Status API to query the transaction status for a case that Owner Verification has been completed.

32 \~ 34. The Ordering VASP receives the transaction status inquiry via the Callback API.

35 \~ 38. The Ordering VASP checks the transaction status and returns the result.

39. The Beneficiary VASP can confirm the deposit and notify the user based on the transaction status.