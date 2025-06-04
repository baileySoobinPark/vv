---
title: Scenarios and Flows
excerpt: >-
  본 문서에서는 TravelRule 구현을 위한 필수 연동 과정을 포함하는 Best Practice 시나리오와 선택 구현사항인
  Screening 시나리오 및 세부 Flow를 소개합니다. 각 VASP는 시나리오별 Flow를 참고하여 요구사항에 맞추어 TravelRule
  솔루션을 도입할 수 있습니다. 
deprecated: false
hidden: false
metadata:
  robots: index
---
## TravelRule Best Practice

Sequence Diagram 1은 TravelRule 기본 프로토콜 구현의 Best Practice Flow를 나타냅니다. TravelRule 프로토콜은 크게 다음 4개의 단계로 진행됩니다: (1)수신 VASP 선택 (2)수신자 계정 검증 (3)수신자 검증 (4)트랜잭션 실행. Sequence Diagram 1은 각 단계를 점선 박스로 구분하여 표시합니다.

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
  <div class="scenario-title">1. 송신자의 자산 출금 신청 및 수신 VASP 선택</div>
  <ol class="step-list">
    <li class="step-item">
      <div class="step-badge">1</div>
      <div class="step-content">사용자(송신자)가 송신 VASP에 출금을 요청합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">2</div>
      <div class="step-content">송신 VASP는 사용자에게 수신 VASP를 선택하도록 하기 위해 Enclave의 <code>Get VASP list</code> API를 호출합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">3</div>
      <div class="step-content">송신 VASP의 Enclave는 중앙 서버에 수신 VASP 목록 조회를 요청합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">4</div>
      <div class="step-content">중앙 서버는 사용 가능한 수신 VASP 목록을 반환합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">5</div>
      <div class="step-content">Enclave는 목록을 수신한 후 VASP 백엔드로 전달합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">6</div><div class="step-badge">7</div>
      <div class="step-content">사용자에게 수신 VASP 목록을 표시한 후 사용자가 수신 VASP를 선택합니다.</div>
    </li>
  </ol>
</div>

<div class="scenario-section">
  <div class="scenario-title">2. 계정 검증 (Account Verification)</div>
  <ol class="step-list">
    
    <!-- 정보 수집 -->
    <div class="subsection-title">수신 계정 및 수신자 정보 수집</div>
    <li class="step-item">
      <div class="step-badge">8</div>
      <div class="step-content">사용자는 Travel Rule 준수를 위해 송신 VASP가 요청하는 정보를 입력합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">9</div>
      <div class="step-content">송신 VASP는 사용자 입력과 내부 정보를 조합하여 Enclave의 <code>User Account Verification API</code>를 호출합니다. 요청에는 수신 VASP ID, 키 유형, 티커, 전송 정보, 수신자 주소 등이 포함됩니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">10</div>
      <div class="step-content">Enclave는 요청된 키 유형에 해당하는 공개키가 캐시에 존재하는지 확인합니다. 유효한 키가 없으면 키 교환 절차(11–16단계)를 진행합니다.</div>
    </li>

    <!-- 키 교환 (선택적) -->
    <div class="subsection-title">키 교환 (공개키가 캐시에 존재하지 않는 경우 수행)</div>
    <li class="step-item">
      <div class="step-badge">11</div>
      <div class="step-content">송신 VASP의 Enclave는 중앙 서버를 통해 수신 VASP의 공개키를 요청합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">12</div>
      <div class="step-content">중앙 서버는 해당 요청을 수신 VASP Enclave로 전달합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">13</div>
      <div class="step-content">수신 VASP Enclave는 캐시된 공개키가 없다면 새 키 쌍을 생성합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">14</div>
      <div class="step-content">수신 VASP는 생성된 공개키를 중앙 서버를 통해 송신 VASP로 전달합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">15</div><div class="step-badge">16</div>
      <div class="step-content">송신 VASP Enclave는 공개키를 수신한 뒤 키 유형에 따라 캐싱합니다.</div>
    </li>

    <!-- 검증 요청 -->
    <div class="subsection-title">검증 요청</div>
    <li class="step-item">
      <div class="step-badge">17</div>
      <div class="step-content">송신 VASP Enclave는 수신 VASP의 공개키로 민감 정보를 암호화합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">18</div>
      <div class="step-content">요청 서명을 위해 Enclave는 키 쌍을 생성하거나 기존 키를 조회합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">19</div>
      <div class="step-content">암호화된 수신자 주소 및 관련 정보가 중앙 서버를 통해 수신 VASP로 전송됩니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">20</div>
      <div class="step-content">중앙 서버는 요청을 수신 VASP Enclave로 전달합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">21</div>
      <div class="step-content">수신 VASP Enclave는 비공개키로 요청 데이터를 복호화합니다.</div>
    </li>

    <!-- 계정 검증 로직 -->
    <div class="subsection-title">수신 계정 검증 로직</div>
    <li class="step-item">
      <div class="step-badge">22</div>
      <div class="step-content">수신 VASP Enclave는 VASP 백엔드의 <code>Verify User Account API</code>를 호출하여 주소 소유 여부를 확인합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">23</div>
      <div class="step-content">수신 VASP는 해당 주소가 VASP 소유 주소인지 여부를 검증합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">24</div>
      <div class="step-content">검증 결과가 수신 VASP Enclave에 반환됩니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">25</div>
      <div class="step-content">수신 VASP Enclave는 결과를 중앙 서버에 전달합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">26</div>
      <div class="step-content">중앙 서버는 결과를 송신 VASP Enclave로 전달합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">27</div>
      <div class="step-content">송신 VASP Enclave는 결과를 송신 VASP 백엔드로 전달합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">28</div>
      <div class="step-content">검증 결과가 <code>DENIED</code>인 경우 사용자는 안내를 받고 절차가 종료되며, <code>VERIFIED</code>인 경우 사용자 검증 단계로 진행됩니다.</div>
    </li>
  </ol>
</div>
<div class="scenario-section">
  <div class="scenario-title">3. 사용자 검증 (User Verification)</div>
  <ol class="step-list">

    <!-- 정보 암호화 및 요청 -->
    <div class="subsection-title">정보 암호화 및 요청</div>
    <li class="step-item">
      <div class="step-badge">29</div>
      <div class="step-content">계정이 검증되면, 송신 VASP는 사용자 검증 API 절차를 시작합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">30</div>
      <div class="step-content">Enclave는 수신 VASP의 공개키를 사용하여 민감한 사용자 정보를 암호화합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">31</div><div class="step-badge">32</div>
      <div class="step-content">암호화된 요청은 중앙 서버로 전송됩니다. 중앙 서버는 비동기 처리를 위해 해당 요청건에 대한 고유한 검증 UUID를 발급하고 비동기 처리를 위해 요청을 큐에 등록합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">33</div><div class="step-badge">34</div>
      <div class="step-content">송신 VASP Enclave는 중앙 서버로부터 반환된 UUID를 데이터베이스에 저장합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">35</div>
      <div class="step-content">Enclave는 백엔드의 검증 요청 응답으로 UUID를 반환합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">36</div>
      <div class="step-content">중앙 서버는 검증 요청을 수신 VASP로 전달합니다.(비동기 방식)</div>
    </li>
    <li class="step-item">
      <div class="step-badge">37</div>
      <div class="step-content">수신 VASP Enclave는 개인키로 요청내 암호화 필드들을 복호화합니다.</div>
    </li>

    <!-- 검증 처리 -->
    <div class="subsection-title">검증</div>
    <li class="step-item">
      <div class="step-badge">38</div>
      <div class="step-content">수신 VASP Enclave는 백엔드의 <code>Verify User API</code>를 호출하여 사용자 정보를 검증합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">39</div>
      <div class="step-content">수신 VASP는 비즈니스 로직에 따라 사용자를 검증합니다. 이 과정에서 컴플라이언스 또는 리스크 스크리닝 등 선택적 절차가 포함될 수 있습니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">40</div>
      <div class="step-content">검증이 완료되면 Enclave로 검증 결과와 추가 정보 또는 오류 메시지를 반환합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">41</div><div class="step-badge">42</div>
      <div class="step-content">Enclave는 데이터베이스에 UUID와 연관된 기록을 갱신하고, 송신 VASP의 공개키로 결과를 암호화 한 뒤 중앙 서버로 결과를 반환합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">43</div>
      <div class="step-content">중앙 서버는 송신 VASP Enclave에 결과를 전달합니다.(비동기 방식)</div>
    </li>
    <li class="step-item">
      <div class="step-badge">44</div>
      <div class="step-content">송신 VASP Enclave는 결과를 복호화하여 데이터베이스에 저장합니다.</div>
    </li>

    <!-- 콜백 및 추가 확인 -->
    <div class="subsection-title">콜백 및 추가 확인</div>
    <li class="step-item">
      <div class="step-badge">45</div>
      <div class="step-content">송신 VASP Enclave는 Callback API를 호출하여 검증 결과와 추가 정보를 전달합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">46</div>
      <div class="step-content">송신 VASP는 해당 정보를 바탕으로 추가적인 확인 절차(선택적 스크리닝 등)를 수행할 수 있습니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">47</div>
      <div class="step-content">Callback API 수신 시, 송신 VASP는 항상 <code>200 OK</code> 응답을 반환하여 수신 완료를 명시해야 합니다.</div>
    </li>

    <!-- 취소 및 오류 처리 -->
    <div class="subsection-title">취소 및 오류 처리</div>
    <li class="step-item">
      <div class="step-badge">48</div>
      <div class="step-content">검증 결과가 <code>DENIED</code>이거나, 송신 VASP가 절차를 중단하기로 결정한 경우 사용자에게 안내 후 절차를 종료합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">49</div>
      <div class="step-content">검증이 <code>VERIFIED</code>된 경우에도, 송신자가 중단을 원하거나 내부 오류 또는 고위험 사유로 인해 절차를 종료할 수 있습니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">50</div>
      <div class="step-content">송신 VASP는 이 사실을 수신 VASP에 통보해야 합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">51</div>
      <div class="step-content">송신 VASP Enclave는 오류 보고서를 중앙 서버로 전송합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">52</div>
      <div class="step-content">중앙 서버는 해당 보고를 수신 VASP Enclave로 전달합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">53</div>
      <div class="step-content">Enclave는 Callback API를 호출하여 수신 VASP에 검증 실패를 통지합니다.</div>
    </li>

    <!-- 프로세스 완료 -->
    <div class="subsection-title">프로세스 완료</div>
    <li class="step-item">
      <div class="step-badge">54</div>
      <div class="step-content">모든 검증이 성공하면 송신 VASP는 사용자에게 통보하고 트랜잭션 실행 단계로 넘어갑니다.</div>
    </li>
  </ol>
</div>
<div class="scenario-section">
  <div class="scenario-title">4. 트랜잭션 실행</div>
  <ol class="step-list">

    <div class="subgroup-title">트랜잭션 생성 및 제출</div>
    <li class="step-item">
      <div class="step-badge">54</div>
      <div class="step-content">송신 VASP는 송신자 계정의 자산을 수신자에게 전송하는 블록체인 트랜잭션을 생성하고 제출합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">55</div>
      <div class="step-content">필요한 경우, 블록체인 특성에 따라 트랜잭션의 finality를 추적하는 기능을 구현할 수 있습니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">56</div>
      <div class="step-content">트랜잭션 해시를 확보한 직후, 송신 VASP는 <code>Report Transaction Result</code> API를 호출하여 트랜잭션 해시를 수신 VASP에 전달합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">57</div>
      <div class="step-content">Enclave는 트랜잭션 해시를 검증 UUID에 매핑하여 내부 데이터베이스를 갱신합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">58</div><div class="step-badge">59</div>
      <div class="step-content">Report 데이터가 중앙 서버를 통해 수신 VASP Enclave로 전달됩니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">60</div>
      <div class="step-content">수신 VASP Enclave는 트랜잭션 해시를 검증 UUID에 매핑하여 저장하고, <code>Callback API</code>를 호출하여 트랜잭션 정보를 수신 VASP에 전달합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">61</div>
      <div class="step-content">수신 VASP가 <code>200 OK</code> 응답을 반환하면 트랜잭션 프로세스는 완료됩니다.</div>
    </li>

    <div class="subgroup-title">예외 처리: 트랜잭션 보고 누락</div>
    <li class="step-item">
      <div class="step-badge">62</div>
      <div class="step-content">수신 VASP가 온체인 입금을 감지했으나 관련 트랜잭션 보고를 받지 못한 경우, Enclave의<code>Check Transaction Result</code> API를 호출합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">63</div><div class="step-badge">64</div>
      <div class="step-content">요청은 중앙 서버를 통해 송신 VASP Enclave로 전달됩니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">65</div>
      <div class="step-content">송신 VASP Enclave는 검증 UUID에 매핑된 트랜잭션 해시를 조회한 뒤 백엔드로 전달하여 온체인 상태를 확인합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">66</div>
      <div class="step-content">송신 VASP는 보고된 트랜잭션 해시의 온체인 처리 상태를 확인합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">67</div><div class="step-badge">68</div><div class="step-badge">69</div><div class="step-badge">70</div>
      <div class="step-content">트랜잭션의 처리 상태를 Enclave를 통해 수신 VASP에게 반환합니다.</div>
    </li>

  </ol>
</div>
`}</HTMLBlock>

<br />

## Screening (Optional)

리스크 기반 자산 이동 검증을 수행하고자 하는 경우 VASP는 필요에 따라 3rd Party 스크리닝 서비스를 연동할 수 있습니다. 스크리닝 서비스는 특정 지갑 주소, 트랜잭션 또는 개인에 대한 리스크 점수를 제공하여 이를 위험도 평가 기준으로 활용 가능합니다. 대표적인 3rd Party API로는 Chainalysis의 Sanction API, Chainalysis의 KYT API, Refinitiv의 World-Check One(WCO) API가 있습니다.

각 API는 리스크 평가의 대상과 목적이 다르므로, VASP는 자사 컴플라이언스 및 운영 요건에 따라 적합한 서비스를 선택하여 연동할 수 있습니다. VerifyVASP Enclave는 검증 UUID를 기반으로 외부 리스크 평가 API를 요청하기 위한 인터페이스를 제공하여 검증 완료 트랜잭션에 대한 추가 리스크 평가를 간소화하고 데이터 이중 관리를 방지합니다.

For detailed instructions on using each API, refer to the [enclave screening API documentation](ref:travelrule-Chainalysis-Sanction) .

### 1. Chainalysis Sanction API Integration

<Image align="center" border={false} caption="Sequence Diagram 2. Chainalysis Sanction API integration flow for risk assessment" src="https://files.readme.io/6c2f368995602e6a646743e3e28ee61a067a9aff95941a7315a9afebe1e87947-tr_solution_2.webp" />

Sequence Diagram 2는 송신 VASP와 수신 VASP가 Chainalysis Sanction API를 연동하여 리스크 평가를 수행하는 과정을 보여줍니다. Sanction API는 사용자 검증 요청 이후에 호출되어야 하며, 트랜잭션 실행 전에 사전 스크리닝 용도로 활용하는 것을 권장합니다. 세부 절차는 아래와 같습니다.

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
    <li class="step-item"><div class="step-badge">3</div><div class="step-content">Enclave는 Chainalysis 서버와 통신하여 스크리닝을 완료한 뒤 결과를 수신하여 안전하게 저장합니다.</div></li>
    <li class="step-item"><div class="step-badge">4</div><div class="step-content">Enclave가 결과를 VASP백엔드로 전달합니다.</div></li>
  </ol>

  <div class="info-note">
    📘 참고: 이 API는 수신 VASP가 송신 VASP로부터 사용자 검증 요청을 수신한 이후 호출됩니다. Sanction API 결과에 따라 송신자 주소가 고위험으로 판단되면, 수신 VASP는 해당 사용자 검증 결과를 DENIED로 응답할 수 있습니다.
  </div>

  <ol class="step-list">
    <li class="step-item"><div class="step-badge">5</div><div class="step-content">Enclave는 평가 결과를 Enclave 데이터베이스의 <b>Sanction Results Table</b>에 저장합니다.</div></li>
  </ol>

  <div class="sub-section-title">송신 VASP 측 리스크 평가</div>
  <ol class="step-list">
    <li class="step-item"><div class="step-badge">6</div><div class="step-content">송신 VASP도 수신자 주소에 대해 동일한 방식으로 리스크 평가를 수행할 수 있습니다.</div></li>
    <li class="step-item"><div class="step-badge">7</div><div class="step-content">평가 대상이 수신자 주소로 변경될 뿐 전체 흐름은 수신 VASP 측 시나리오와 동일한 Flow를 통해 진행됩니다.</div></li>
  </ol>

  <div class="info-note">
    📘 참고: 수신자 주소가 고위험으로 판단될 경우, 송신 VASP는 자산 출금을 중단하거나 취소할 수 있습니다. 단, 자산 출금 취소시 반드시 수신 VASP에게 ERROR REPORT를 전송하여 취소 사실을 알려야 합니다.
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

송신 VASP는 트랜잭션 실행에 앞서 KYT API를 호출하여 수신 주소의 위험도를 평가할 수 있습니다. 또한 트랜잭션을 실행한 후에는 해당 트랜잭션 식별자를 제출하여 트랜잭션 자체의 위험도를 평가할 수 있습니다. 수신 VASP는 트랜잭션 결과 Report를 수신하거나 입금 트랜잭션을 감지한 후 트랜잭션 위험도 평가를 수행하기 위해 KYT API를 호출할 수 있습니다. 세부 절차는 아래와 같습니다.

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
      <div class="step-content">Chainalysis 서비스로부터 수신자 주소에 대한 위험도 평가 결과를 조회합니다. 다이어그램에서서는 평가 요청에 대한 동기 응답으로 표현되어 있으나, 실제로는 Enclave의 결과 조회 API 호출을 통한 비동기 조회 방식으로 동작합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">5</div>
      <div class="step-content">Enclave가 조회한 평가 결과를 데이터베이스에 저장합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">6</div>
      <div class="step-content">Enclave는 VASP의 Callback API를 호출하여 위험도 평가 결과를 전달합니다.</div>
    </li>
  </ol>

  <div class="info-note">
    📘 <strong>참고:</strong><br>
    KYT API 결과로 수신자 주소가 고위험(high-risk)으로 판단될 경우, Originating VASP는 자산 이전을 취소할 수 있습니다. 이 경우, 반드시 Beneficiary VASP에 ERROR REPORT를 전송하여 취소 사실을 통보해야 합니다.
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
      <div class="step-content">Enclave는 VASP의 Callback API를 호출하여 평가 결과를 전달하고, 리스크 평가 절차를 종료합니다.</div>
    </li>
  </ol>

  <div class="sub-section-title">입금 VASP 측 리스크 평가 - 입금 트랜잭션</div>
  <ol class="step-list">
    <li class="step-item">
      <div class="step-badge">21</div> ~<div class="step-badge">28</div>
      <div class="step-content">수신 VASP 측에서도 트랜잭션 Report를 수신하거나 온체인 입금을 감지한 후 해당 트랜잭션에 대한 위험도 평가를 수행할 수 있습니다. Flow는 송신 VASP측 트랜잭션 위험도 평가와 동일합니다. 트랜잭션 위험도 평가를 통해 자산 이전 과정의 보안성과 규제 대응 능력을 향상시킬 수 있습니다.</div>
    </li>
  </ol>
</div>
`}</HTMLBlock>

<br />

### 3. Refinitiv WCO API Integration

<Image align="center" border={false} caption="Sequence Diagram 3. Refinitiv WCO API integration flow for risk assessment" src="https://files.readme.io/e20fb9a58375cd5403148ec1a6ea7d4f462964c57fd23f3c576893f81391fe22-tr_solution_4.webp" />

Sequence Diagram 4는 송신 VASP와 수신 VASP가 각각 Refinitiv WCO API를 활용하여 위험도 평가를 수행하는 과정을 보여줍니다. WCO API는 개인 식별 정보(PII)를 기반으로 송신자 및 수신자와 같은 개인에 대한 위험도를 평가할 수 있도록 지원합니다. 세부 절차는 아래와 같습니다.

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
      <div class="step-content">Enclave가 VASP 백엔드의 Callback API를 호출하여 결과를 전달합니다.</div>
    </li>
  </ol>

  <div class="info-note">
    📘 <strong>참고:</strong><br>
    WCO API가 수신자의 PII를 고위험군으로 판단한 경우, 송신 VASP는 자산 전송을 취소할 수 있습니다. 전송 취소시 송신 VASP는 오류 보고(ERROR REPORT)를 통해 수신 VASP에 취소 사실을 반드시 통지해야 합니다.
  </div>

  <div class="sub-section-title">수신 VASP 측 위험도 평가 - 송신자 PII</div>
  <ol class="step-list">
    <li class="step-item">
      <div class="step-badge">8</div>
      <div class="step-content">수신 VASP도 송신자의 PII를 대상으로 WCO API 기반 위험도 평가를 수행할 수 있습니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">9</div>~ <div class="step-badge">14</div>
      <div class="step-content">위험도 평가는 수신 VASP가 사용자 검증 요청을 수신한 이후, 사용자 검증 결과를 반환하기 이전에 수행되어야 합니다.</div>
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
      <div class="step-badge">17</div>
      <div class="step-content">송신 VASP는 트랜잭션 결과 Report API를 호출하여 수신 VASP에 트랜잭션 결과를 전달합니다.</div>
    </li>
    <li class="step-item">
      <div class="step-badge">18</div>
      <div class="step-content">수신 VASP는 보고된 트랜잭션 해시를 확인 후 VASP 정책에 따라 필요한 확인 절차들을 수행합니다.</div>
    </li>
  </ol>
</div>
`}</HTMLBlock>