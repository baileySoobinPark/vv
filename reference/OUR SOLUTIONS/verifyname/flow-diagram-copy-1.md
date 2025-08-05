---
title: Scenarios and Flows
excerpt: >-
  This section introduces the Post-Verification and Pre-Verification scenarios
  for the VerifyName 2.0 protocol, along with the related API flows. Each VASP
  can choose the appropriate scenario based on the counterparty VASP’s Travel
  Rule regulatory obligations.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Post-Verification - From Non-Obliged VASP to Travel Rule Obliged VASP

In a **Post-Verification** scenario, the blockchain transaction is executed first, and the Beneficiary VASP detects the incoming transaction before requesting verification.\
This scenario typically occurs when a non-obliged VASP sends assets without pre-verification.
The Beneficiary VASP can use the VerifyName protocol to confirm whether the originator and beneficiary account owners are the same before crediting the deposit.

<br />

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
  <div class="scenario-title">1. Withdrawal Request & Transaction Execution</div>
  <ol class="step-list">
    <li class="step-item"><div class="step-badge">1</div>
      <div class="step-content">The originator requests a withdrawal from the Ordering VASP.</div></li>
    <li class="step-item"><div class="step-badge">2</div>
      <div class="step-content">The non-obliged Ordering VASP executes the withdrawal transaction on the blockchain without performing beneficiary/account verification.</div></li>
  </ol>
</div>
<div class="scenario-section">
  <div class="scenario-title">2. Post-Verification Request by Beneficiary VASP</div>
  <ol>
    <li class="step-item"><div class="step-badge">3</div>
      <div class="step-content">Once the transaction is confirmed, the Beneficiary VASP detects the incoming deposit to the beneficiary address.</div></li>
    <div class="subsection-title">Identify the Ordering VASP</div>
    <li class="step-item"><div class="step-badge">4</div>
      <div class="step-content">The obliged Beneficiary VASP calls the Enclave’s <code>List VASP API</code> to identify the Ordering VASP before applying the deposit.</div></li>
    <li class="step-item"><div class="step-badge">5</div>
      <div class="step-content">The Beneficiary VASP’s Enclave requests the VASP list from the Central Server.</div></li>
    <li class="step-item"><div class="step-badge">6</div>
      <div class="step-content">The Central Server returns the list of VASPs.</div></li>
    <li class="step-item"><div class="step-badge">7</div>
      <div class="step-content">The Enclave forwards the list to the Beneficiary VASP backend.</div></li>
    <li class="step-item"><div class="step-badge">8</div><div class="step-badge">9</div>
      <div class="step-content">The beneficiary (user) selects the Ordering VASP from the list provided by the Beneficiary VASP.</div></li>
    
    <div class="subsection-title">Send Verification Request</div>
    <li class="step-item"><div class="step-badge">10</div>
      <div class="step-content">The Beneficiary VASP calls the <code>Owner Verification API</code> to request that the Ordering VASP confirm whether the originator’s name and date of birth match the beneficiary account owner’s.</div></li>
    <li class="step-item"><div class="step-badge">11</div><div class="step-badge">12</div>
      <div class="step-content">The Beneficiary VASP’s Enclave generates a random salt and, per the VerifyName protocol, creates a hash using the salt, name, and DOB.</div></li>
    <li class="step-item"><div class="step-badge">13</div>
      <div class="step-content">The Enclave encrypts the salt with the Ordering VASP’s public key. If the public key is not cached or is unavailable, the Enclave performs key exchange and caching as in the <a href="https://verifyvasp.readme.io/reference/flow-diagram-copy#/travelrule-best-practice"> TravelRule 프로토콜과 같은 방식 </a> TravelRule protocol.</div></li>
    <li class="step-item"><div class="step-badge">14</div>
      <div class="step-badge">15</div><div class="step-content"> The verification request, including the generated hash, is sent via the Central Server to the Ordering VASP’s Enclave.</div></li>
    
    <div class="subsection-title">Transaction and Originator Data Matching</div>
    <li class="step-item"><div class="step-badge">16</div>
      <div class="step-content"> The Ordering VASP’s Enclave calls the backend’s <code>VerifyName API</code> to request transaction verification. The request does not include personal data—only the transaction hash, asset ticker, and network information.</div></li>
    <li class="step-item"><div class="step-badge">17</div>
      <div class="step-content">The Ordering VASP checks its database for a matching transaction record.</div></li>
    <li class="step-item"><div class="step-badge">18</div>
      <div class="step-content">If found, the Ordering VASP identifies the originator and retrieves their name and DOB from the database.</div></li>
    <li class="step-item"><div class="step-badge">19</div>
      <div class="step-content">The Ordering VASP returns the transaction details and originator information to the Enclave.</div></li>
    <li class="step-item"><div class="step-badge">20</div><div class="step-badge">21</div>
      <div class="step-content">The Enclave decrypts the salt using the Ordering VASP’s private key and generates a hash from the salt, name, and DOB.</div></li>
    <li class="step-item"><div class="step-badge">22</div>
      <div class="step-content">The hash is compared with the one received from the Beneficiary VASP..</div></li>
    <li class="step-item"><div class="step-badge">23</div>
      <div class="step-badge">24</div><div class="step-badge">25</div>
      <div class="step-content">The transaction verification result and hash comparison result are returned via the Central Server to the Beneficiary VASP.</div></li>
	</ol>
</div>

<div class="scenario-section">
  <div class="scenario-title">3. Verification Result Report</div>
  <ol class="step-list">
    <li class="step-item"><div class="step-badge">26</div>
      <div class="step-content">The Beneficiary VASP determines the account ownership verification result and whether to credit the deposit.</div></li>
    <li class="step-item"><div class="step-badge">27</div><div class="step-badge">28</div>
      <div class="step-badge">29</div><div class="step-badge">30</div><div class="step-content">The Beneficiary VASP sends the result via the Enclave’s <code>Owner Verification Result Report API</code> to the Ordering VASP. The Ordering VASP’s Enclave calls the backend’s <code>Callback API(OWNER_VERIFICATION_RESULT_REPORT)</code> to deliver the result.</div></li>
    <li class="step-item"><div class="step-badge">31</div>
      <div class="step-badge">32</div><div class="step-content"><strong>[dd]</strong> 공유된 최종 검증 결과에 따라 양쪽 VASP는 입출금 반영을 확정하고, 사용자에게 결과를 안내할 수 있습니다.</div></li>
  </ol>
</div>
`}</HTMLBlock>

<br />

<br />

## Pre-Verification - From Travel Rule Obliged VASP to Travel Rule Non-Obliged VASP

사후 검증 시나리오와 반대로, VerifyName 프로토콜의 사전 검증(Pre-Verification) 시나리오는 송신 VASP가 Travel Rule 규제를 준수하는 VASP인 경우에 해당됩니다. 송신 VASP는 자산 이전을 위한 블록체인 트랜잭션을 실행하기에 앞서, 수신인이 송신인 정보와 일치하는지 검증을 선제적으로 진행한 뒤 그 결과에 따라 출금 여부를 판단할 수 있습니다.

<Image align="center" border={false} caption="The Beneficiary VASP can confirm the deposit and notify the user based on the transaction status." src="https://files.readme.io/704a1473f6993c17679876446ed395a7b6985a1ff3cdf303fdd318ecf6be619d-sequence_diagram_2-Pre_Verification.drawio.svg" />

<HTMLBlock>{`
<div class="scenario-section">
  <div class="scenario-title">1. 수신 계좌 소유주 검증</div>
  <ol class="step-list">
    <li class="step-item"><div class="step-badge">1</div>
      <div class="step-content">사용자(송신자)가 송신 VASP로 디지털 자산 출금을 요청합니다.</div></li>
    <li class="step-item"><div class="step-badge">2</div>
      <div class="step-content">송신 VASP는 송신자와 수신 계좌 소유주의 동일인 여부를 검증하기 위해 Enclave의 <code>Owner Verification API</code>를 호출합니다. 요청에는 디지털 자산 정보와 수신자의 이름, 생년월일, 주소가 포함됩니다.</div></li>
    <li class="step-item"><div class="step-badge">3</div><div class="step-badge">4</div>
      <div class="step-content">송신 VASP의 Enclave는 랜덤 Salt를 생성한 뒤, VerifyName 프로토콜에 따라 Salt와 송신자의 이름, 생년월일 정보를 입력값으로 하는 Hash를 생성합니다.</div></li>
    <li class="step-item"><div class="step-badge">5</div>
      <div class="step-content">생성한 Salt값을 수신 VASP의 공개키로 암호화합니다.</div></li>
    <li class="step-item"><div class="step-badge">6</div><div class="step-badge">7</div>
      <div class="step-content">Enclave는 생성된 Hash값과 암호화된 Salt를 포함한 소유주 검증 요청을 중앙 서버를 통해 수신 VASP로 전달합니다.</div></li>
    <li class="step-item"><div class="step-badge">8</div>
      <div class="step-content">수신 VASP의 Enclave가 VASP 백엔드로 <code>VerifyName API</code>를 호출하여 검증 프로세스를 시작합니다. 이때 수신 VASP 백엔드로는 Hash를 제외한 수신 주소, 전송하고자 하는 자산의 티커 및 네트워크 정보만 전달됩니다.</div></li>
    <li class="step-item"><div class="step-badge">9</div>
      <div class="step-content">수신 VASP 서버는 자산 정보와 수신 주소를 데이터베이스와 대조하여 해당 VASP에서 발급한 입금 주소인지 여부를 확인합니다.</div></li>
    <li class="step-item"><div class="step-badge">10</div>
      <div class="step-content">유효한 입금 주소인 경우 해당 주소의 소유주를 식별한 뒤, 데이터베이스에서 해당 사용자의 이름과 생년월일을 조회합니다.</div></li>
    <li class="step-item"><div class="step-badge">11</div>
      <div class="step-content">수신 VASP 백엔드는 주소 검증 결과와 함께 수신자의 이름 및 생년월일을 Enclave에 반환합니다.</div></li>
    <li class="step-item"><div class="step-badge">12</div>
      <div class="step-content">Enclave는 개인키로 암호화된 Salt를 복호화합니다.</div></li>
    <li class="step-item"><div class="step-badge">13</div>
      <div class="step-content">복호화된 Salt와 수신자의 이름, 생년월일을 입력값으로 Hash를 생성합니다.</div></li>
    <li class="step-item"><div class="step-badge">14</div>
      <div class="step-content">생성된 해시값과 송신 VASP로부터 전달받은 해시값의 일치 여부를 비교합니다.</div></li>
    <li class="step-item"><div class="step-badge">15</div><div class="step-badge">16</div><div class="step-badge">17</div>
      <div class="step-content">Enclave는 수신 계좌 검증 결과와 해시값 비교 결과를 중앙 서버를 통해 송신 VASP로 반환합니다.</div></li>
  </ol>
</div>

<div class="scenario-section">
  <div class="scenario-title">2. 검증 결과 Report</div>
  <ol class="step-list">
    <li class="step-item"><div class="step-badge">18</div>
      <div class="step-content">송신 VASP는 반환받은 검증 결과를 바탕으로 최종 검증 결과(송금 실행 여부)를 판단하고 결과를 수신 VASP에게 Report해야 합니다.</div></li>
    <li class="step-item"><div class="step-badge">19</div>
      <div class="step-content">Enclave의 <code>Owner Verification Result Report API</code>를 호출하여 결과를 수신 VASP에 공유합니다.</div></li>
    <li class="step-item"><div class="step-badge">20</div><div class="step-badge">21</div><div class="step-badge">22</div>
      <div class="step-content">수신 VASP의 Enclave는 VASP 백엔드의 <code>Callback API(OWNER_VERIFICATION_RESULT_REPORT)</code>를 호출하여 Report 된 결과를 전달합니다.</div></li>
    <li class="step-item"><div class="step-badge">23</div>
      <div class="step-content"><strong>[선택]</strong> 송신 VASP의 검증 결과가 <code>실패</code>인 경우, 사용자에게 출금이 불가함을 출금 취소 사유와 함께 안내할 수 있습니다.</div></li>
    <li class="step-item"><div class="step-badge">24</div>
      <div class="step-content">송신 VASP의 검증 결과가 <code>성공</code>인 경우, 디지털 자산 전송을 위한 트랜잭션을 실행합니다.</div></li>
  </ol>
</div>

<div class="scenario-section">
  <div class="scenario-title">3. 트랜잭션 실행 결과 Report</div>
  <ol class="step-list">
    <li class="step-item"><div class="step-badge">25</div>
      <div class="step-content">트랜잭션 실행 후, 트랜잭션 Hash가 생성됩니다.</div></li>
    <li class="step-item"><div class="step-badge">26</div>
      <div class="step-content"><code>Owner Verification Transaction Report API</code>를 호출하여 해당 트랜잭션 해시를 기반으로 실행 결과를 Report합니다.</div></li>
    <li class="step-item"><div class="step-badge">27</div><div class="step-badge">28</div><div class="step-badge">29</div>
      <div class="step-content">수신 VASP Enclave는 백엔드의 <code>Callback API(OWNER_VERIFICATION_TX_REPORT)</code>를 통해 트랜잭션 실행 보고 결과를 전달합니다.</div></li>
    <li class="step-item"><div class="step-badge">30</div>
      <div class="step-content">결과를 전달받은 수신 VASP는 해당 결과를 바탕으로 입금 여부를 확정하고 사용자에게 안내할 수 있습니다.</div></li>
  </ol>
</div>
`}</HTMLBlock>