---
title: Scenarios and Flows
excerpt: >-
  This section introduces the Best Practice scenario required for TravelRule
  implementation, along with optional Screening scenarios and detailed flows.
  Follow the flow diagrams to complete the TravelRule integration process
  according to your VASP’s requirements.
deprecated: false
hidden: false
metadata:
  robots: index
---
## TravelRule Best Practice

Sequence Diagram 1 illustrates the Best Practice flow for implementing the TravelRule protocol.\
The TravelRule process consists of four main stages:

1. Select Beneficiary VASP
2. Verify Beneficiary Account
3. Verify Beneficiary Information
4. Transfer Assets

<Image align="center" border={false} caption="Sequence Diagram 1. TravelRule Best practice" src="https://files.readme.io/125494277f7e9aa4eec30651b9de394e590c20766dece1100861095183930c7f-tr_flow_diagram.png" />

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
  <div class="scenario-title">1. Originator Requests Withdrawal & Selects Beneficiary VASP</div>
  <ol class="step-list">
    <li class="step-item">
      <div class="step-badge">1</div>
      <div class="step-content">The Originator requests a withdrawal from the Ordering VASP.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">2</div>
      <div class="step-content">The Ordering VASP calls the List VASP API in the Enclave to retrieve a list of available beneficiary VASPs.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">3</div>
      <div class="step-content">The Ordering VASP’s Enclave requests the beneficiary VASP list from the Central Server.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">4</div>
      <div class="step-content">The Central Server returns the list of available beneficiary VASPs.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">5</div>
      <div class="step-content">The Enclave forwards the list to the Ordering VASP backend.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">6</div><div class="step-badge">7</div>
      <div class="step-content">The list of beneficiary VASPs is displayed to the user, and the user selects one.</div>
    </li>
  </ol>
</div>

<div class="scenario-section">
  <div class="scenario-title">2. Account Verification</div>
  <ol class="step-list">
    
    <!-- 정보 수집 -->
    <div class="subsection-title">Collect Beneficiary Account & User Information</div>
    <li class="step-item">
      <div class="step-badge">8</div>
      <div class="step-content">To comply with the Travel Rule, the Originator enters the required beneficiary information requested by the Ordering VASP.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">9</div>
      <div class="step-content">The Ordering VASP combines the user input with internal information and calls the <code>User Account Verification API</code> in the Enclave. The request includes: beneficiary VASP ID, key type, ticker, transfer information, and beneficiary address.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">10</div>
      <div class="step-content">The Enclave checks if a valid public key for the specified key type is already cached. If no valid key exists, the Key Exchange procedure (Steps 11–16) is executed.</div>
    </li>

    <!-- 키 교환 (선택적) -->
    <div class="subsection-title">Key Exchange (Performed only if the public key is not cached)</div>
    <li class="step-item">
      <div class="step-badge">11</div>
      <div class="step-content">The Ordering VASP’s Enclave requests the beneficiary VASP’s public key from the Central Server.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">12</div>
      <div class="step-content">The Central Server forwards this request to the beneficiary VASP’s Enclave.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">13</div>
      <div class="step-content">If no public key is cached, the beneficiary VASP’s Enclave generates a new key pair.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">14</div>
      <div class="step-content">The beneficiary VASP sends the generated public key to the Ordering VASP via the Central Server.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">15</div><div class="step-badge">16</div>
      <div class="step-content">The Ordering VASP’s Enclave receives the public key and caches it according to the key type policy.</div>
    </li>

    <!-- 검증 요청 -->
    <div class="subsection-title">Send Verification Request</div>
    <li class="step-item">
      <div class="step-badge">17</div>
      <div class="step-content">The Ordering VASP’s Enclave encrypts sensitive information using the beneficiary VASP’s public key.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">18</div>
      <div class="step-content">For request signing, the Enclave generates or retrieves an existing key pair.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">19</div>
      <div class="step-content">The encrypted beneficiary address and related information are sent to the Central Server.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">20</div>
      <div class="step-content">The Central Server forwards the request to the beneficiary VASP’s Enclave.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">21</div>
      <div class="step-content">The beneficiary VASP’s Enclave decrypts the request data using its private key.</div>
    </li>

    <!-- 계정 검증 로직 -->
    <div class="subsection-title">Beneficiary Account Verification Logic</div>
    <li class="step-item">
      <div class="step-badge">22</div>
      <div class="step-content">The beneficiary VASP’s Enclave calls the backend’s <code>Verify User Account API</code> to check address ownership.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">23</div>
      <div class="step-content">The beneficiary VASP verifies whether the address belongs to the VASP.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">24</div>
      <div class="step-content">The verification result is returned to the beneficiary VASP’s Enclave.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">25</div>
      <div class="step-content">The beneficiary VASP’s Enclave sends the result to the Central Server.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">26</div>
      <div class="step-content">The Central Server forwards the result to the Ordering VASP’s Enclave.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">27</div>
      <div class="step-content">The Ordering VASP’s Enclave passes the result to the Ordering VASP backend.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">28</div>
      <div class="step-content">If the result is <code>DENIED</code>, the user is notified and the process ends. If the result is <code>VERIFIED</code>, the process continues to the Beneficiary Information Verification stage.</div>
    </li>
  </ol>
</div>
<div class="scenario-section">
  <div class="scenario-title">3. User Verification</div>
  <ol class="step-list">

    <!-- 정보 암호화 및 요청 -->
    <div class="subsection-title">Encrypt Information and Send Request</div>
    <li class="step-item">
      <div class="step-badge">29</div>
      <div class="step-content">Once the account is verified, the Ordering VASP calls the <code>User Verification API</code> in the Enclave to start user verification.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">30</div>
      <div class="step-content">The Enclave encrypts sensitive user information using the Beneficiary VASP’s public key.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">31</div><div class="step-badge">32</div>
      <div class="step-content">The encrypted request is sent to the Central Server. The Central Server issues a unique verification UUID for asynchronous processing and adds the request to the queue.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">33</div><div class="step-badge">34</div>
      <div class="step-content">The Ordering VASP’s Enclave stores the UUID in its database.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">35</div>
      <div class="step-content">The Enclave returns the UUID to the Ordering VASP backend in the verification request response.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">36</div>
      <div class="step-content">The Central Server forwards the verification request to the Beneficiary VASP asynchronously.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">37</div>
      <div class="step-content">The Beneficiary VASP’s Enclave decrypts the encrypted fields in the request using its private key.</div>
    </li>

    <!-- 검증 처리 -->
    <div class="subsection-title">Verification</div>
    <li class="step-item">
      <div class="step-badge">38</div>
      <div class="step-content">The Beneficiary VASP’s Enclave calls the backend’s <code>Verify User API</code> to validate the user information.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">39</div>
      <div class="step-content">The Beneficiary VASP verifies the user according to its policy, optionally including compliance or risk screening procedures.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">40</div>
      <div class="step-content">Once verification is complete, the backend returns the result with any additional information or error messages to the Enclave.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">41</div><div class="step-badge">42</div>
      <div class="step-content">The Enclave updates the database record associated with the UUID, encrypts the result using the Ordering VASP’s public key, and sends it to the Central Server.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">43</div>
      <div class="step-content">The Central Server forwards the result to the Ordering VASP’s Enclave asynchronously.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">44</div>
      <div class="step-content">The Ordering VASP’s Enclave decrypts the result and stores it in the database.</div>
    </li>

    <!-- 콜백 및 추가 확인 -->
    <div class="subsection-title">Callback and Additional Checks</div>
    <li class="step-item">
      <div class="step-badge">45</div>
      <div class="step-content">The Ordering VASP’s Enclave calls the backend’s <code>Callback API</code> to deliver the verification result and any additional information.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">46</div>
      <div class="step-content">The Ordering VASP may perform additional checks (e.g., optional screening) based on the received information.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">47</div>
      <div class="step-content">When receiving a Callback API request, the Ordering VASP must always return <code>200 OK</code> to confirm receipt.</div>
    </li>

    <!-- 취소 및 오류 처리 -->
    <div class="subsection-title">Cancellation and Error Handling</div>
    <li class="step-item">
      <div class="step-badge">48</div>
      <div class="step-content">If the verification result is <code>DENIED</code>, or if the Ordering VASP decides to terminate the process, the user is notified and the process ends. Even if the result is <code>VERIFIED</code>, the process can still be terminated at the sender’s request, or due to internal errors or high-risk conditions.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">49</div>
      <div class="step-content">If the withdrawal process is stopped, the Ordering VASP calls the Enclave’s <code>Report Error API</code> to notify the counterparty.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">50</div>
      <div class="step-content">The Ordering VASP’s Enclave sends the error report to the Central Server.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">51</div>
      <div class="step-content">The Central Server forwards the report to the Beneficiary VASP’s Enclave.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">52</div>
      <div class="step-content">The Enclave calls the backend’s <code>Callback API</code> to notify that the process has been terminated.</div>
    </li>

    <!-- 프로세스 완료 -->
    <div class="subsection-title">Process Completion</div>
    <li class="step-item">
      <div class="step-badge">53</div>
      <div class="step-content">If all verifications are successful, the Ordering VASP notifies the user and proceeds to the transaction execution stage.</div>
    </li>
  </ol>
</div>
<div class="scenario-section">
  <div class="scenario-title">4. Transaction Execution</div>
  <ol class="step-list">

    <div class="subsection-title">Create and Submit Transaction</div>
    <li class="step-item">
      <div class="step-badge">54</div>
      <div class="step-content">The Ordering VASP creates and submits a blockchain transaction to transfer the assets from the originator to the beneficiary.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">55</div>
      <div class="step-content">If required, implement logic to track transaction finality depending on the blockchain’s characteristics.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">56</div>
      <div class="step-content">Once the transaction hash is obtained, the Ordering VASP calls the Enclave’s <code>Report Transaction Result API</code> to send the transaction hash to the Beneficiary VASP.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">57</div>
      <div class="step-content">The Enclave maps the transaction hash to the verification UUID and updates its internal database.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">58</div><div class="step-badge">59</div>
      <div class="step-content">The report data is sent via the Central Server to the Beneficiary VASP’s Enclave.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">60</div>
      <div class="step-content">The Beneficiary VASP’s Enclave maps the transaction hash to the verification UUID, stores it, and calls the backend’s <code>Callback API</code> to deliver the transaction information.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">61</div>
      <div class="step-content">Once the Beneficiary VASP returns <code>200 OK</code>, the transaction process is complete.</div>
    </li>

    <div class="subsection-title">Exception: Missing Transaction Report</div>
    <li class="step-item">
      <div class="step-badge">62</div>
      <div class="step-content">If the Beneficiary VASP detects an on-chain deposit but has not received the corresponding transaction report, it calls the Enclave’s <code>Check Transaction Status API</code>.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">63</div><div class="step-badge">64</div>
      <div class="step-content">The request is sent via the Central Server to the Ordering VASP’s Enclave.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">65</div>
      <div class="step-content">The Ordering VASP’s Enclave calls the backend’s <code>Check Transaction Status API</code> to confirm the on-chain status of the transaction mapped to the verification UUID.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">66</div>
      <div class="step-content">The Ordering VASP identifies the transaction corresponding to the UUID and checks its on-chain processing status.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">67</div><div class="step-badge">68</div><div class="step-badge">69</div><div class="step-badge">70</div>
      <div class="step-content">The transaction status is returned to the Beneficiary VASP via the Enclave.</div>
    </li>

  </ol>
</div>
`}</HTMLBlock>

<br />

## Screening (Optional)

If a VASP chooses to perform risk-based verification, it can integrate with third-party screening services. These services provide a risk score for specific wallet addresses, transactions, or individuals, which can be used as part of a risk assessment framework.

Common third-party APIs include:

* Chainalysis Sanction API
* Chainalysis KYT API
* Refinitiv World-Check One (WCO) API

Each API has a different target and purpose for risk assessment. VASPs should select and integrate the service that best fits their compliance and operational requirements.

The **VerifyVASP Enclave** provides an interface to call external risk assessment APIs using a **verification UUID**, simplifying additional risk assessments for completed verification transactions and preventing duplicate data management.

For detailed usage instructions, refer to the [enclave screening API documentation](ref:travelrule-Chainalysis-Sanction) .

<br />

### 1. Chainalysis Sanction API Integration

<Image align="center" border={false} caption="Sequence Diagram 2. Chainalysis Sanction API integration flow for risk assessment" src="https://files.readme.io/6c2f368995602e6a646743e3e28ee61a067a9aff95941a7315a9afebe1e87947-tr_solution_2.webp" />

Sequence Diagram 2 shows how an Ordering VASP and a Beneficiary VASP integrate the Chainalysis Sanction API to perform risk assessments. The Sanction API should be called after the user verification request and is recommended for use before asset transfers to evaluate risk.

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

  .sub-section-title {
    font-weight: bold;
    font-size: 15px;
    margin: 16px 0 10px;
    border-left: 4px solid #1364FF;
    padding-left: 8px;
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
    margin-right: 12px;
    flex-shrink: 0;
  }

  .step-content {
    flex: 1;
    font-size: 14px;
    line-height: 1.6;
  }

  .info-note {
    background-color: #f1f7ff;
    border-left: 4px solid #007bff;
    padding: 10px 12px;
    margin: 12px 0;
    font-size: 13px;
    color: #333;
  }
</style>

<div class="scenario-section">
  <div class="scenario-title">Sanction API 기반 리스크 평가</div>

  <div class="sub-section-title">수신 VASP 측 리스크 평가</div>
  <ol class="step-list">
    <li class="step-item"><div class="step-badge">1</div><div class="step-content">수신 VASP는 Enclave API를 호출하여 송신자 주소에 대한 Sanction API 기반 리스크 평가를 요청할 수 있습니다. 요청에는 Verification UUID가 포함되어야 합니다.</div></li>
    <li class="step-item"><div class="step-badge">2</div><div class="step-content">Enclave는 requestId 및 Chainalysis API 요청 본문(Body)을 생성합니다.</div></li>
    <li class="step-item"><div class="step-badge">3</div><div class="step-content">Enclave는 Chainalysis 서버와 통신하여 스크리닝을 완료한 뒤 결과를 수신합니다.</div></li>
    <li class="step-item"><div class="step-badge">4</div><div class="step-content">Enclave가 결과를 VASP백엔드로 전달합니다.</div></li>
    <li class="step-item"><div class="step-badge">5</div><div class="step-content">Enclave는 평가 결과를 Enclave 데이터베이스의 <b>Sanction Results Table</b>에 저장합니다.</div></li>
  </ol>

  <div class="sub-section-title">송신 VASP 측 리스크 평가</div>
  <ol class="step-list">
    <li class="step-item"><div class="step-badge">6</div><div class="step-content">송신 VASP도 수신자 주소에 대해 동일한 방식으로 리스크 평가를 수행할 수 있습니다.</div></li>
    <li class="step-item"><div class="step-badge">7</div> ~ <div class="step-badge">10</div><div class="step-content">평가 대상이 수신자 주소로 변경될 뿐 전체 흐름은 수신 VASP 측 시나리오와 동일한 Flow를 통해 진행됩니다.</div></li>
  </ol>

  <div class="info-note">
    📘 참고: 수신자 주소가 고위험으로 판단될 경우, 송신 VASP는 자산 출금을 중단하거나 취소할 수 있습니다. 단, 자산 출금 취소시 반드시 수신 VASP에게 Error Report를 전송하여 취소 사실을 알려야 합니다.
  </div>

  <div class="sub-section-title">트랜잭션 실행</div>
  <ol class="step-list">
    <li class="step-item"><div class="step-badge">11</div><div class="step-content">Sanction 결과에 따라 각 계정이 고위험 계정으로 판단되지 않는 경우 송신 VASP는 Best Practice와 같이 블록체인 트랜잭션을 실행 단계로 진입할 수 있습니다.</div></li>
    <li class="step-item"><div class="step-badge">12</div><div class="step-content">블록체인 상에서 자산 전송을 완료한 후, 송신 VASP는 <b>Report Transaction Result API</b>를 호출하여 트랜잭션 해시를 수신 VASP에 전달합니다.</div></li>
  </ol>
</div>
`}</HTMLBlock>

<br />

<br />

### 2. Chainalysis KYT API Integration

<Image align="center" border={false} caption="Sequence Diagram 3. Chainalysis KYT API integration flow for risk assessment" src="https://files.readme.io/2ac080e6cc5469ea7f1d6769eceb099cb13cb44aaed4b95becc9f69d38e42b2c-tr_solution_3.avif" />

Sequence Diagram 3은 송신 VASP와 수신 VASP가 Chainalysis KYT API를 연동하여 위험도 평가를 수행하는 절차를 보여줍니다. KYT API는 특정 주소 또는 트랜잭션을 대상으로 한 위험도 평가를 지원합니다.

송신 VASP는 자산 이전에 앞서 KYT API를 호출하여 수신 주소의 위험도를 평가할 수 있습니다. 또한 자산을 이전한 후에는 Txhash를 제출하여 트랜잭션의 위험도를 평가할 수 있습니다. 수신 VASP는 트랜잭션 결과 Report를 수신하거나 입금 트랜잭션을 감지한 후 트랜잭션 위험도 평가를 수행하기 위해 KYT API를 호출할 수 있습니다. 세부 절차는 아래와 같습니다.

<HTMLBlock>{`
<div class="scenario-section">
  <div class="scenario-title">KYT API 기반 리스크 평가</div>

  <div class="sub-section-title">송신 VASP 측 리스크 평가 - 수신자 주소</div>
  <ol class="step-list">
    <li class="step-item">
      <div class="step-badge">1</div>
      <div class="step-content">송신 VASP는 사용자 검증 요청을 보낸 이후, Enclave API를 호출하여 수신자 주소에 대한 위험도 평가를 요청합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">2</div>
      <div class="step-content">Enclave는 Chainalysis의 KYT API 호출 시 필요한 RequestId 및 RequestBody를 생성합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">3</div>
      <div class="step-content">Enclave가 Chainalysis 서버에 평가 요청을 전송합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">4</div>
      <div class="step-content">Chainalysis 서비스로부터 수신자 주소에 대한 위험도 평가 결과를 조회합니다. 다이어그램에서는 평가 요청에 대한 동기 응답으로 표현되어 있으나, 실제로는 Enclave가 주기적으로 결과 조회 API를 호출하는 방식으로 동작합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">5</div>
      <div class="step-content">Enclave가 조회한 평가 결과를 데이터베이스에 저장합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">6</div>
      <div class="step-content">Enclave는 VASP 백엔드의 <code>Callback API</code>를 호출하여 위험도 평가 결과를 전달합니다.</div>
    </li>
  </ol>

  <div class="info-note">
    📘 <strong>참고:</strong><br>
    KYT API 결과로 수신자 주소가 고위험(high-risk)으로 판단될 경우, Originating VASP는 자산 이전을 취소할 수 있습니다. 이 경우, 반드시 Beneficiary VASP에 Error Report를 전송하여 취소 사실을 통보해야 합니다.
  </div>

  <ol class="step-list">
    <li class="step-item">
      <div class="step-badge">7</div>~ <div class="step-badge">14</div>
      <div class="step-content"> 수신 주소가 리스크가 낮다고 판단된 경우, 송신 VASP는 Best Practice Flow와 같이 자산 이전 및 결과 보고 절차를 재개합니다.</div>
    </li>
  </ol>

  <div class="sub-section-title">송신 VASP 측 리스크 평가 - 출금 트랜잭션</div>
  <ol class="step-list">
    <li class="step-item">
      <div class="step-badge">15</div>
      <div class="step-content">자산 이전 후 트랜잭션 해시가 확보되면, 송신 VASP는 Enclave API를 호출하여 해당 트랜잭션에 대한 위험도 평가를 요청할 수 있습니다.<br>※ 호출 전 반드시 Report Transaction Result API가 선행되어야 합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">16</div>
      <div class="step-content">Enclave는 Chainalysis API 요청에 필요한 RequestId 및 RequestBody를 생성합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">17</div>
      <div class="step-content">Enclave가 Chainalysis 트랜잭션 위험도 평가 API 요청을 전송합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">18</div>
      <div class="step-content">Chainalysis 서버는 트랜잭션에 대한 평가 결과를 반환합니다. 주소 위험도 평가와 마찬가지로, 다이어그램에는 동기 응답으로 표현되어 있으나 실제로는 Enclave의 결과 조회 API 호출을 통한 비동기 방식으로 결과를 확인합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">19</div>
      <div class="step-content">Enclave가 조회환 결과를 데이터베이스에 저장합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">20</div>
      <div class="step-content">Enclave는 VASP 백엔드의 <code>Callback API</code>를 호출하여 평가 결과를 전달하고, 리스크 평가 절차를 종료합니다.</div>
    </li>
  </ol>

  <div class="sub-section-title">입금 VASP 측 리스크 평가 - 입금 트랜잭션</div>
  <ol class="step-list">
    <li class="step-item">
      <div class="step-badge">21</div> ~ <div class="step-badge">28</div>
      <div class="step-content">수신 VASP 측에서도 트랜잭션 Report를 수신하거나 온체인 입금을 감지한 후 해당 트랜잭션에 대한 위험도 평가를 수행할 수 있습니다. Flow는 송신 VASP측 트랜잭션 위험도 평가와 동일합니다. 트랜잭션 위험도 평가를 통해 자산 이전 과정의 보안성과 규제 대응 능력을 향상시킬 수 있습니다.</div>
    </li>
  </ol>
</div>
`}</HTMLBlock>

<br />

### 3. Refinitiv WCO API Integration

<Image align="center" border={false} caption="Sequence Diagram 3. Refinitiv WCO API integration flow for risk assessment" src="https://files.readme.io/e20fb9a58375cd5403148ec1a6ea7d4f462964c57fd23f3c576893f81391fe22-tr_solution_4.webp" />

Sequence Diagram 4는 송신 VASP와 수신 VASP가 각각 Refinitiv WCO API를 활용하여 위험도 평가를 수행하는 과정을 보여줍니다. WCO API는 개인 식별 정보(PII)를 기반으로 송수신인 개인에 대한 위험도를 평가할 수 있도록 지원합니다. 세부 절차는 아래와 같습니다.

<HTMLBlock>{`
<div class="scenario-section">
  <div class="scenario-title">WCO API를 활용한 PII 기반 위험도 평가</div>

  <div class="sub-section-title">송신 VASP 측 위험도 평가 - 수신자 PII</div>
  <ol class="step-list">
    <li class="step-item">
      <div class="step-badge">1</div>
      <div class="step-content">송신 VASP 백엔드가 수신자의 PII에 대해 위험도 평가를 수행하기 위해 Enclave의 Refinitiv WCO API를 호출합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">2</div>
      <div class="step-content">Enclave가 WCO API 요청에 필요한 RequestId 및 RequestBody를 생성합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">3</div>
      <div class="step-content">Enclave는 수신자의 PII를 포함한 위험도 평가 요청을 Refinitiv 서버에 전송합니다. 다이어그램에서는 단일 요청처럼 보이지만, 실제로는 여러 단계의 API 호출로 이루어진 비동기 방식으로 동작합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">4</div>
      <div class="step-content">Refinitiv 서버가 수신자 PII의 위험도를 평가하고 결과를 반환합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">5</div>
      <div class="step-content">Enclave는 위험도 평가 결과를 데이터베이스에 저장합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">6</div><div class="step-badge">7</div>
      <div class="step-content">Enclave가 VASP 백엔드의 <code>Callback API</code>를 호출하여 결과를 전달합니다.</div>
    </li>
  </ol>

  <div class="info-note">
    📘 <strong>참고:</strong><br>
    WCO API가 수신자의 PII를 고위험군으로 판단한 경우, 송신 VASP는 자산 전송을 취소할 수 있습니다. 전송 취소시 송신 VASP는 오류 보고(Error Report)를 통해 수신 VASP에 취소 사실을 반드시 통지해야 합니다.
  </div>

  <div class="sub-section-title">수신 VASP 측 위험도 평가 - 송신자 PII</div>
  <ol class="step-list">
    <li class="step-item">
      <div class="step-badge">8</div>
      <div class="step-content">수신 VASP도 송신자의 PII를 대상으로 WCO API 기반 위험도 평가를 수행할 수 있습니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">9</div>~ <div class="step-badge">14</div>
      <div class="step-content">위험도 평가는 수신 VASP가 사용자 검증 결과를 반환한 이후 수행되어야 합니다.</div>
    </li>
  </ol>

  <div class="sub-section-title">트랜잭션 실행</div>
  <ol class="step-list">
    <li class="step-item">
      <div class="step-badge">15</div>
      <div class="step-content">WCO의 위험도 판단 결과 저위험군으로 분류된 경우, 송신 VASP는 Best Practice와 같이 자산 전송 프로세스를 재개합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">16</div>
      <div class="step-content">블록체인 상에서 실제 자산 전송 트랜잭션을 실행합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">17</div> ~ <div class="step-badge">20</div>
      <div class="step-content">송신 VASP는 트랜잭션 결과 Report API를 호출하여 수신 VASP에 트랜잭션 결과를 전달합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">21</div>
      <div class="step-content">수신 VASP는 보고된 트랜잭션 해시를 확인 후 VASP 정책에 따라 필요한 확인 절차들을 수행합니다.</div>
    </li>
  </ol>
</div>
`}</HTMLBlock>