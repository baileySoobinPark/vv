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

VerifyName 프로토콜의 사후 검증(Post-Verification) 시나리오는, 송금을 위한 블록체인 트랜잭션이 먼저 실행된 후 이를 감지한 수신 VASP 측에서 검증을 요청하는 경우 발생합니다. 주로 송금 VASP가 Travel Rule 규제 대상 VASP가 아닌 경우 별도의 검증 없이 송금을 실행하는 경우에 해당합니다. 수신 VASP는 컴플라이언스 준수를 위해 미확인 입금건에 대해 송신자가 수신 계정 소유주와 일치하는지 여부를 VerifyName 프로토콜을 사용하여 검증할 수 있습니다.

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
  <div class="scenario-title">1. 출금 신청 및 트랜잭션 실행</div>
  <ol class="step-list">
    <li class="step-item"><div class="step-badge">1</div>
      <div class="step-content">사용자(송신자)가 송신 VASP에 출금을 요청합니다.</div></li>
    <li class="step-item"><div class="step-badge">2</div>
      <div class="step-content">Travel Rule 규제 대상이 아닌 송신 VASP는 별도의 수신자/수신자 계좌 검증 없이 출금 트랜잭션을 불록체인 상에 실행하여 자산을 전송합니다.</div></li>
  </ol>
</div>
<div class="scenario-section">
  <div class="scenario-title">2. 수신 VASP 측 사후 검증(Post-Verification) 요청</div>
  <ol>
    <li class="step-item"><div class="step-badge">3</div>
      <div class="step-content">트랜잭션이 확정(Confirmed)되면 수신 VASP가 수신자 주소로의 입금을 감지합니다.</div></li>
    <div class="subsection-title">송신 VASP 확인</div>
    <li class="step-item"><div class="step-badge">4</div>
      <div class="step-content">Travel Rule 규제 대상인 수신 VASP는 입금을 반영하기에 앞서 송신자와 수신자의 일치 여부를 사후 검증을 통해 확인할 수 있습니다. 가장 먼저, 송신 VASP 확인을 위해 Enclave의 <code>List VASP API</code>를 호출합니다.</div></li>
    <li class="step-item"><div class="step-badge">5</div>
      <div class="step-content">수신 VASP의 Enclave는 중앙 서버에 수신 VASP 목록 조회를 요청합니다.</div></li>
    <li class="step-item"><div class="step-badge">6</div>
      <div class="step-content">중앙 서버는 VASP 목록을 반환합니다.</div></li>
    <li class="step-item"><div class="step-badge">7</div>
      <div class="step-content">Enclave는 목록을 수신한 후 송신 VASP 백엔드로 전달합니다.</div></li>
    <li class="step-item"><div class="step-badge">8</div><div class="step-badge">9</div>
      <div class="step-content">사용자(수신자)는 수신 VASP가 제시한 목록으로부터 해당 입금건의 송신 VASP를 선택합니다. </div></li>
    
    <div class="subsection-title">수신 VASP의 검증 요청</div>
    <li class="step-item"><div class="step-badge">10</div>
      <div class="step-content">수신 VASP는 <code>Owner Verification API</code>를 호출하여 송신 VASP측으로 송신자의 이름과 생년월일이 수신 계좌 소유주의 것과 일치하는지에 대한 검증을 요청합니다. 요청은 자산 정보와 수신자의 이름, 생년월일을 포함합니다.</div></li>
    <li class="step-item"><div class="step-badge">11</div><div class="step-badge">12</div>
      <div class="step-content">수신 VASP의 Enclave는 랜덤 Salt를 생성한 뒤 VerifyName 프로토콜에 따라 Salt와 송신자의 이름, 생년월일 정보를 입력값으로 하는 Hash를 생성합니다.</div></li>
    <li class="step-item"><div class="step-badge">13</div>
      <div class="step-content">수신 VASP의 Enclave는 Hash 생성에 사용한 Salt를 송신 VASP의 공개키로 암호화합니다. 이때 만약 송신 VASP의 공개키가 캐싱되어있지 않거나 사용할 수 없는 상태인 경우, <a href="https://verifyvasp.readme.io/reference/flow-diagram-copy#/travelrule-best-practice"> TravelRule 프로토콜과 같은 방식 </a> 으로 Enclave간 키 교환 및 캐싱을 진행합니다. </div></li>
    <li class="step-item"><div class="step-badge">14</div>
      <div class="step-badge">15</div><div class="step-content"> 11번 Step에서 생성된 해시값을 포함한 검증 요청이 중앙 서버를 통해 송신 VASP의 Enclave로 전달됩니다.</div></li>
    
    <div class="subsection-title">트랜잭션 및 송신자 정보 비교 검증</div>
    <li class="step-item"><div class="step-badge">16</div>
      <div class="step-content">송신 VASP Enclave는 VASP 백엔드의 <code>VerifyName API</code>를 호출하여 트랜잭션 검증을 요청합니다. 트랜잭션 검증 요청은 수신자의 개인정보를 포함하지 않으며 오직 트랜잭션 해시, 자산 티커, 네트워크 정보만을 포함합니다.</div></li>
    <li class="step-item"><div class="step-badge">17</div>
      <div class="step-content">송신 VASP는 요청에 포함된 정보들로부터 해당 트랜잭션과 관련된 전송 이력의 존재 여부를 데이터베이스와 대조하여 확인합니다.</div></li>
    <li class="step-item"><div class="step-badge">18</div>
      <div class="step-content">전송건이 특정되는 경우, 해당 전송건의 송신자를 식별하고 해당 사용자의 이름과 생년월일을 데이터베이스로부터 조회합니다.</div></li>
    <li class="step-item"><div class="step-badge">19</div>
      <div class="step-content">송신 VASP는 트랜잭션 조회 결과 및 송신자 이름, 생년월일 정보를 API 응답으로 Enclave에 전달합니다.</div></li>
    <li class="step-item"><div class="step-badge">20</div><div class="step-badge">21</div>
      <div class="step-content">Enclave는 송신 VASP의 개인키로 암호화된 Salt를 복호화 한 뒤, VerifyName 프로토콜에 따라 Salt와 송신자의 이름, 생년월일 정보를 입력값으로 하는 Hash를 생성합니다.</div></li>
    <li class="step-item"><div class="step-badge">22</div>
      <div class="step-content">생성된 Hash값이 수신 VASP로부터 받은 Hash값과 일치하는지 여부를 비교합니다.</div></li>
    <li class="step-item"><div class="step-badge">23</div>
      <div class="step-badge">24</div><div class="step-badge">25</div>
      <div class="step-content">송신 VASP의 Enclave가 트랜잭션 검증 결과와 Hash 비교 결과를 중앙 서버를 통해 수신 VASP로 반환합니다.</div></li>
	</ol>
</div>

<div class="scenario-section">
  <div class="scenario-title">검증 결과 Report</div>
  <ol class="step-list">
    <li class="step-item"><div class="step-badge">26</div>
      <div class="step-content">수신 VASP는 송신 VASP로부터 전달받은 검증 결과를 바탕으로 계정 소유주 검증 결과 및 이에 따른 입금 반영 여부를 확정합니다.</div></li>
    <li class="step-item"><div class="step-badge">27</div><div class="step-badge">28</div>
      <div class="step-badge">29</div><div class="step-badge">30</div><div class="step-content">수신 VASP는 계정 소유주 검증 결과를 Enclave의 <code>Report Verification Result API</code>를 통해 송신 VASP로 전달해야 합니다. Report를 전달받은 송신 VASP의 Enclave는 송신 VASP 백엔드의 <code>Callback API</code>를 호출하여 해당 결과를 전달합니다.</div></li>
    <li class="step-item"><div class="step-badge">31</div>
      <div class="step-badge">32</div><div class="step-content"><strong>[선택]</strong> 공유된 최종 검증 결과에 따라 양쪽 VASP는 입출금 반영을 확정하고, 사용자에게 결과를 안내할 수 있습니다.</div></li>
  </ol>
</div>
`}</HTMLBlock>

<br />

<br />

## Pre-Verification - From Travel Rule Obliged VASP to Travel Rule Non-Obliged VASP

In contrast to the post-verification case, when the Ordering VASP is a regulated entity, it performs owner verification before executing the transfer.

<Image align="center" border={false} caption="The Beneficiary VASP can confirm the deposit and notify the user based on the transaction status." src="https://files.readme.io/0446271b8557cb4007d6f656d8972440b023a65d5ea6871e346744775979198b-Pre_Verification.svg" />

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
    <li class="step-item"><div class="step-badge">6</div>
      <div class="step-content">Enclave는 생성된 Hash값과 암호화된 Salt를 포함한 소유주 검증 요청을 중앙 서버를 통해 수신 VASP로 전달합니다.</div></li>
    <li class="step-item"><div class="step-badge">7</div>
      <div class="step-content">수신 VASP의 Enclave가 VASP 백엔드로 <code>VerifyName API</code>를 호출하여 검증 프로세스를 시작합니다. 이때 수신 VASP 백엔드로는 Hash를 제외한 수신 주소, 전송하고자 하는 자산의 티커 및 네트워크 정보만 전달됩니다.</div></li>
    <li class="step-item"><div class="step-badge">8</div>
      <div class="step-content">수신 VASP 서버는 자산 정보와 수신 주소를 데이터베이스와 대조하여 해당 VASP에서 발급한 입금 주소인지 여부를 확인합니다.</div></li>
    <li class="step-item"><div class="step-badge">9</div>
      <div class="step-content">유효한 입금 주소인 경우 해당 주소의 소유주를 식별한 뒤, 데이터베이스에서 해당 사용자의 이름과 생년월일을 조회합니다.</div></li>
    <li class="step-item"><div class="step-badge">10</div>
      <div class="step-content">수신 VASP 백엔드는 주소 검증 결과와 함께 수신자의 이름 및 생년월일을 Enclave에 전달합니다.</div></li>
    <li class="step-item"><div class="step-badge">11</div>
      <div class="step-content">Enclave는 개인키로 암호화된 Salt를 복호화합니다.</div></li>
    <li class="step-item"><div class="step-badge">12</div>
      <div class="step-content">복호화된 Salt와 수신자의 이름, 생년월일을 입력값으로 Hash를 생성합니다.</div></li>
    <li class="step-item"><div class="step-badge">13</div>
      <div class="step-content">생성된 해시값과 송신 VASP로부터 전달받은 해시값의 일치 여부를 비교합니다.</div></li>
    <li class="step-item"><div class="step-badge">14</div>
      <div class="step-content">Enclave는 수신 계좌 검증 결과와 해시값 비교 결과를 중앙 서버를 통해 송신 VASP로 반환합니다.</div></li>
  </ol>
</div>

<div class="scenario-section">
  <div class="scenario-title">2. 검증 결과 Report</div>
  <ol class="step-list">
    <li class="step-item"><div class="step-badge">15</div>
      <div class="step-content">송신 VASP는 반환받은 검증 결과를 바탕으로 최종 검증 결과(송금 실행 여부)를 판단하고 결과를 수신 VASP에게 Report해야 합니다.</div></li>
    <li class="step-item"><div class="step-badge">16</div>
      <div class="step-content">Enclave의 <code>Report Verification Result API</code>를 호출하여 결과를 수신 VASP에 공유합니다.</div></li>
    <li class="step-item"><div class="step-badge">17</div>
      <div class="step-content">수신 VASP의 Enclave는 VASP 백엔드의 <code>Callback API</code>를 호출하여 Report 된 결과를 전달합니다.</div></li>
    <li class="step-item"><div class="step-badge">18</div>
      <div class="step-content"><strong>[선택]</strong> 송신 VASP의 검증 결과가 <code>실패</code>인 경우, 사용자에게 출금이 불가함을 출금 취소 사유와 함께 안내할 수 있습니다.</div></li>
    <li class="step-item"><div class="step-badge">19</div>
      <div class="step-content">송신 VASP의 검증 결과가 <code>성공</code>인 경우, 디지털 자산 전송을 위한 트랜잭션을 실행합니다.</div></li>
  </ol>
</div>

<div class="scenario-section">
  <div class="scenario-title">3. 트랜잭션 실행 후 결과 Report</div>
  <ol class="step-list">
    <li class="step-item"><div class="step-badge">20</div>
      <div class="step-content">트랜잭션 실행 후, 트랜잭션 Hash가 생성됩니다.</div></li>
    <li class="step-item"><div class="step-badge">21</div>
      <div class="step-content"><code>Owner Verification Transaction Result API</code>를 호출하여 해당 트랜잭션 해시를 기반으로 실행 결과를 Report합니다.</div></li>
    <li class="step-item"><div class="step-badge">22</div>
      <div class="step-content">수신 VASP Enclave는 백엔드의 <code>Callback API</code>를 통해 트랜잭션 실행 보고 결과를 전달합니다.</div></li>
    <li class="step-item"><div class="step-badge">23</div>
      <div class="step-content">결과를 전달받은 수신 VASP는 해당 결과를 바탕으로 입금 여부를 확정하고 사용자에게 안내할 수 있습니다.</div></li>
  </ol>
</div>

<div class="scenario-section">
  <div class="scenario-title">4. Report 누락에 따른 상태 조회</div>
  <ol class="step-list">
    <li class="step-item"><div class="step-badge">24</div>
      <div class="step-content">만약 별도의 트랜잭션 실행 Report를 수신하지 못했음에도 입금이 탐지된 경우, 수신 VASP는 <code>Check Transaction Status API</code>를 호출하여 트랜잭션 상태를 조회할 수 있습니다.</div></li>
    <li class="step-item"><div class="step-badge">25</div>
      <div class="step-content">해당 요청은 중앙서버와 송신 VASP의 Enclave를 통해 송신 VASP 백엔드의  <code>Callback API</code>를 통해 전달됩니다.</div></li>
    <li class="step-item"><div class="step-badge">26</div>
      <div class="step-content">송신 VASP는 전달받은 트랜잭션 Hash로 트랜잭션 상태를 확인한 후, 결과를 반환합니다.</div></li>
    <li class="step-item"><div class="step-badge">27</div>
      <div class="step-content">수신 VASP는 해당 결과를 바탕으로 입금 반영 여부를 판단하고 사용자에게 안내할 수 있습니다.</div></li>
  </ol>
</div>
`}</HTMLBlock>