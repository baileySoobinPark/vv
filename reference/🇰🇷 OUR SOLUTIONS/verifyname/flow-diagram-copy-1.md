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
## Post-Verification - From Non-Obliged VASP to Travel Rule Obliged VASP

Sequence Diagram 1 illustrates the post-verification flow, detailing how ownership verification between Originator and Beneficiary accounts is conducted after a blockchain transaction is confirmed. This scenario occurs when the unregulated ordering VASP skips the verification process, prompting the regulated Beneficiary VASP to initiate a post-verification request to ensure compliance.

<Image align="center" border={false} caption="Sequence Diagram 1. VerifyName integration flow for unregulated VASP originating withdrawal" src="https://files.readme.io/7e75c4995f2b8b686ba210d9793debccd5c7b8a14dd71545b324fff0d665092b-Post_Verification.svg" />

<HTMLBlock>{`
<style>
  .scenario-section {
    border: 1px dashed #ccc;
    border-radius: 8px;
    padding: 20px;
    margin-bottom: 32px;
    background-color: #fdfdfd;
  }

  .scenario-title {
    font-weight: bold;
    font-size: 16px;
    margin-bottom: 12px;
  }

  .step-list {
    list-style: none;
    padding: 0;
    margin: 0;
  }

  .step-item {
    display: flex;
    align-items: flex-start;
    margin-bottom: 12px;
  }

  .step-badge {
    background-color: #000;
    color: #fff;
    font-weight: bold;
    border-radius: 50%;
    width: 24px;
    height: 24px;
    text-align: center;
    line-height: 24px;
    font-size: 13px;
    margin-right: 5px;
    flex-shrink: 0;
  }

  .step-content {
    flex: 1;
    font-size: 14px;
    line-height: 1.6;
  }

  .subsection-title {
    font-weight: 600;
    font-size: 14px;
    color: #333;
    margin: 24px 0 12px 0;
    padding-left: 4px;
    border-left: 4px solid #007bff;
  }
</style>

<div class="scenario-section">
  <div class="scenario-title">VerifyName Protocol 시퀀스</div>
  <ol class="step-list">
    <li class="step-item"><div class="step-badge">1</div><div class="step-content">Originator는 Ordering VASP를 통해 출금 요청을 수행합니다.</div></li>
    <li class="step-item"><div class="step-badge">2</div><div class="step-content">Ordering VASP가 규제 대상이 아닌 경우, 사전 검증 없이 블록체인에 트랜잭션을 실행하고 자산을 수신자 주소로 전송합니다.</div></li>
    <li class="step-item"><div class="step-badge">3</div><div class="step-content">트랜잭션이 블록에 포함되면, Beneficiary VASP는 수신자 주소로의 입금을 감지합니다.</div></li>
    <li class="step-item"><div class="step-badge">4</div><div class="step-content">Beneficiary VASP는 어떤 VASP가 보냈는지 확인하기 위해 <code>VASP LIST API</code>를 호출합니다.</div></li>
    <li class="step-item"><div class="step-badge">5</div><div class="step-content">Enclave는 중앙 서버에 VASP 목록 요청을 보냅니다.</div></li>
    <li class="step-item"><div class="step-badge">6</div><div class="step-content">중앙 서버는 VASP 목록을 반환합니다.</div></li>
    <li class="step-item"><div class="step-badge">7</div><div class="step-content">Enclave는 VASP 목록을 Beneficiary VASP로 전달합니다.</div></li>
    <li class="step-item"><div class="step-badge">8</div><div class="step-content">Beneficiary VASP는 사용자에게 VASP 목록을 표시합니다.</div></li>
    <li class="step-item"><div class="step-badge">9</div><div class="step-content">사용자가 Ordering VASP를 선택합니다.</div></li>
    <li class="step-item"><div class="step-badge">10</div><div class="step-content">Beneficiary VASP는 <code>Owner Verification API</code>를 호출하여 송신자와 수신자가 동일인인지 확인합니다. 이때 자산 정보와 수신자의 이름, 생년월일이 포함됩니다.</div></li>
    <li class="step-item"><div class="step-badge">11</div><div class="step-content">Beneficiary VASP의 Enclave는 Salt를 생성합니다.</div></li>
    <li class="step-item"><div class="step-badge">12</div><div class="step-content">생성된 Salt로 이름과 생년월일을 해싱합니다.</div></li>
    <li class="step-item"><div class="step-badge">13</div><div class="step-content">Salt는 Ordering VASP의 공개키로 암호화됩니다.</div></li>
    <li class="step-item"><div class="step-badge">14</div><div class="step-badge">15</div><div class="step-content">해싱된 이름/생년월일, 암호화된 Salt가 포함된 검증 요청이 Ordering VASP의 Enclave로 전달됩니다.</div></li>
    <li class="step-item"><div class="step-badge">16</div><div class="step-content">Ordering VASP의 Enclave는 <code>VerifyName API</code>를 호출하여 검증을 시작합니다. 이때 제공되는 정보는 네트워크, 티커, 트랜잭션 해시 등 자산 관련 정보뿐입니다.</div></li>
    <li class="step-item"><div class="step-badge">17</div><div class="step-content">Ordering VASP는 수신된 정보가 자사에서 송신한 트랜잭션과 일치하는지 확인합니다.</div></li>
    <li class="step-item"><div class="step-badge">18</div><div class="step-content">일치하는 항목이 있다면, 트랜잭션 해시로 Originator를 식별하고 이름과 생년월일을 DB에서 조회합니다.</div></li>
    <li class="step-item"><div class="step-badge">19</div><div class="step-content">Ordering VASP는 검증 결과와 함께 Originator의 이름과 생년월일을 Enclave로 전달합니다.</div></li>
    <li class="step-item"><div class="step-badge">20</div><div class="step-content">Enclave는 Ordering VASP의 개인키로 암호화된 Salt를 복호화합니다.</div></li>
    <li class="step-item"><div class="step-badge">21</div><div class="step-content">복호화된 Salt로 Originator의 이름과 생년월일을 해싱합니다.</div></li>
    <li class="step-item"><div class="step-badge">22</div><div class="step-content">해싱된 값과 Beneficiary VASP로부터 받은 값이 일치하는지 비교합니다.</div></li>
    <li class="step-item"><div class="step-badge">23</div><div class="step-badge">24</div><div class="step-badge">25</div><div class="step-content">디지털 자산 전송 검증 결과와 해시 비교 결과를 Beneficiary VASP로 반환합니다.</div></li>
    <li class="step-item"><div class="step-badge">26</div><div class="step-badge">27</div><div class="step-content">Beneficiary VASP는 해당 결과를 바탕으로 최종 검증 결과를 결정하고 보고서를 작성합니다.</div></li>
    <li class="step-item"><div class="step-badge">28</div><div class="step-badge">29</div><div class="step-badge">30</div><div class="step-content">작성된 최종 검증 결과는 <code>Callback API</code>를 통해 Ordering VASP에 전달됩니다.</div></li>
    <li class="step-item"><div class="step-badge">31</div><div class="step-badge">32</div><div class="step-content"><strong>[선택]</strong> 최종 검증 결과에 따라 양 VASP는 입출금 처리를 진행하고, 사용자에게 안내할 수 있습니다.</div></li>
  </ol>
</div>
`}</HTMLBlock>

<br />

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