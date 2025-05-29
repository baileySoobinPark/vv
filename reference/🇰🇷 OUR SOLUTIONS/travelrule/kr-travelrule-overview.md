---
title: Overview
deprecated: false
hidden: true
metadata:
  robots: index
---
## 아키텍처 개요

<Image align="center" border={false} caption="Diagram 1. VerifyVASP Integration Architecture Overview" src="https://files.readme.io/8d27021ec8f83d7f4cc31b17bccc04e96360c65217d142e4733739024c89930b-tr_solution_1.png" />

<br />

### VerifyVASP 중앙 서버 기반 VASP 간 통신

VASP 간 TravelRule 통신은 VerifyVASP 중앙 서버를 통해 중계됩니다. 각 VASP는 입출금 시나리오에 따라 송신자 또는 수신자로 역할을 전환하며, 모든 요청과 응답은 중앙 서버를 거쳐 전달됩니다.

### Enclave 설치 및 연동

Enclave는 VerifyVASP 중앙 서버와의 통신 인터페이스를 제공하는 사전 구축 서버 모듈입니다. Enclave는 Docker 이미지 형태로 배포되며, 모든 VASP는 인프라 내에 Enclave 서버와 Enclave용 데이터베이스를 반드시 설치해야 합니다. 각 VASP의 백엔드는 Enclave 서버와 상호 API 호출을 통해 통신하며, VerifyVASP Central Server API와 직접 통신하지 않습니다.

### 데이터 보안 및 개인정보 보호

Enclave 전용 데이터베이스는 Enclave만 접근할 수 있도록 구성되므로 데이터의 무결성과 격리성을 보장합니다. VASP와 중앙 서버 간의 모든 통신은 HTTPS 기반 암호화 채널을 통해 수행되며, 민감정보를 포함한 모든 데이터는 다층 보안 구조로 보호됩니다.

<br />

## 검증 프로세스 개요

<HTMLBlock>{`
<!DOCTYPE html>

<html lang="ko">
<head>
  <meta charset="UTF-8">
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background-color: #f5f8fb;
      padding: 40px;
      max-width: 1000px;
      margin: auto;
    }


.flow-container {
  display: flex;
  flex-direction: column;
  gap: 40px;
}

.row {
  display: flex;
  flex-wrap: nowrap;
  justify-content: space-between;
  align-items: stretch;
  gap: 12px;
}

.step-box {
  background-color: #ffffff;
  border: 1px solid #1364FF;
  border-radius: 8px;
  flex: 1 1 0;
  max-width: 280px;
  box-shadow: 0 2px 6px rgba(0, 123, 255, 0.15);
  display: flex;
  flex-direction: column;
  min-height: 280px
}

.step-title {
  background-color: #1364FF;
  color: #ffffff;
  padding: 8px 12px;
  font-weight: bold;
  font-size: 14px;
  line-height: 1.2;
  text-align: center;
  border-top-left-radius: 8px;
  border-top-right-radius: 8px;
}

.subtitle {
  font-size: 11px;
  opacity: 0.9;
}

.step-content {
  padding: 16px;
  color: #333333;
  font-size: 14px;
  line-height: 1.5;
}

.step-content b {
  font-weight: 600;
}

.step-content p {
  margin: 0 0 10px;
  padding-left: 1.4em;  /* 왼쪽 여백 확보 */
  position: relative;
}

.step-content p::before {
  content: "•";
  position: absolute;
  left: 0;  /* 왼쪽 여백 내부에서 시작 */
  top: 0;
  font-weight: bold;
  color: #333;
}

.arrow-horizontal {
  align-self: center;
  font-size: 32px;
  color: #007bff;
  font-weight: bold;
}

.arrow-row {
  display: flex;
  justify-content: space-between;
  margin-top: -16px;
  margin-bottom: -16px;
}

.arrow-row div {
  flex: 1 1 0;
  display: flex;
  justify-content: center;
}


  </style>
</head>
<body>
  <div class="flow-container">


<!-- 위쪽 3단계 -->
<div class="row">
  <div class="step-box">
    <div class="step-title">1. 자산 전송 요청<br><span class="subtitle">(Originator → Ordering VASP)</span></div>
    <div class="step-content">
      <p><b>송신 VASP</b>의 사용자(송신자, Originator)가 자산 전송을 요청합니다.</p>
      <p><b>송신 VASP</b>는 TravelRule 프로토콜에서 요구하는 송신자 정보와 수신자(Beneficiary) 정보를 사용자로부터 수집합니다.</p>
    </div>
  </div>
  <div class="step-box">
    <div class="step-title">2. 검증 요청 전송<br><span class="subtitle">(Ordering VASP → Beneficiary VASP)</span></div>
    <div class="step-content">
      <p><b>송신 VASP</b>는 수집된 정보를 기반으로 수신자 검증을 요청합니다. 검증 요청은 Enclave 서버를 통해 VerifyVASP 중앙서버로 전송되어 수신 VASP로 전달됩니다.</p>
      <p>전체 통신 구간에 걸쳐 검증 요청 데이터는 End-to-End 암호화(E2EE)로 보호됩니다.</p>
    </div>
  </div>
  <div class="step-box">
    <div class="step-title">3. 수신 VASP 검증 수행<br><span class="subtitle">(Beneficiary VASP)</span></div>
    <div class="step-content">
      <p>검증 요청을 수신한 <b>수신 VASP</b>는 보유하고 있는 사용자 데이터를 기반으로 수신자 정보를 검증합니다.</p>
    </div>
  </div>
</div>

<!-- 아래쪽 3단계 -->
<div class="row">
  <div class="step-box">
    <div class="step-title">4. 검증 결과 반환<br><span class="subtitle">(Beneficiary VASP → Ordering VASP)</span></div>
    <div class="step-content">
      <p>수신자 검증 결과가 수신 VASP의 Enclave와 VerifyVASP 중앙 서버를 지나 송신 VASP로 전달됩니다.</p>
      <p>이 과정은 동기 또는 비동기 방식으로 처리될 수 있으며, 각 Flow 및 API 명세는 관련 문서를 통해 확인할 수 있습니다.</p>
    </div>
  </div>
  <div class="step-box">
    <div class="step-title">5. 트랜잭션 실행<br><span class="subtitle">(Ordering VASP)</span></div>
    <div class="step-content">
      <p>검증 결과가 정상인 경우 송신 VASP가 블록체인에서 출금 트랜잭션을 실행합니다.</p>
    </div>
  </div>
  <div class="step-box">
    <div class="step-title">6. 트랜잭션 실행 결과 리포트<br><span class="subtitle">(Ordering VASP → Beneficiary VASP)</span></div>
    <div class="step-content">
      <p>송신 VASP는 Report 프로토콜을 통해 실행한 트랜잭션의 ID(Tx Hash)를 수신 VASP에게 전달하여 송금이 완료되었음을 고지합니다.</p>
    </div>
  </div>
</div>


  </div>
</body>
</html>
`}</HTMLBlock>

<br />

## Security

### VASP 간 End-to-End 암호화

VerifyVASP TravelRule 솔루션은 송,수신인 검증 과정에서 교환되는 개인정보를 보호하기 위해 송신 VASP와 수신 VASP 사이 통신 구간에 대해 End-to-End 암호화(E2EE)를 적용합니다. 오직 송신 VASP와 수신 VASP만이 데이터를 복호화 할 수 있으며, VerifyVASP 중앙 서버는 데이터를 복호화 하거나 저장하지 않습니다.

키 교환 및 종단 간 암호화(E2EE) 는 양측 VASP의 Enclave 서버에 의해 자동으로 처리되며 다음과 같은 순서로 진행됩니다. 상세 Flow는 [Flow Diagram](ref:travelrule-flow-diagram) 문서의 수신자 검증 Flow를 참고해주세요.

#### 공개 키 요청 (Ordering VASP → VerifyVASP Central Server → Beneficiary VASP)

* 데이터 암호화가 필요한 시점에, 송신 VASP의 Enclave는 저장된 수신 VASP의 공개키가 있는지 확인합니다.
* 사용 가능한 공개키가 없는 경우, 송신 VASP Enclave는 중앙 서버를 통해 수신 VASP Enclave로 공개키를 요청합니다.

#### 키 쌍 생성 및 공개키 반환 (Beneficiary VASP → VerifyVASP Central Server → Ordering VASP)

* 수신 VASP Enclave는 공개키 설정에 따라 송신 VASP에게 반환할 공개키를 조회하거나 새로운 키 쌍을 생성한 뒤 저장합니다.
* 선택된 공개키가 VerifyVASP 중앙 서버를 통해 송신 VASP Enclave로 반환됩니다.

#### 개인정보 암호화

* 송신 VASP Enclave는 수신 VASP로부터 전달받은 공개키로 개인 정보 필드를 암호화합니다. 암호화된 데이터를 포함한 요청(ex. 수신자 계정 검증 요청)이 VerifyVASP 중앙 서버를 통해 수신 VASP Enclave로 전달된 후, 수신 VASP Enclave는 저장된 비밀키를 조회하여 암호화된 데이터를 복호화한 뒤 검증을 수행합니다.
* <br />

<br />

<br />

1. **검증 요청 시작**
   * 송신 VASP에서 Enclave API를 통해 수신인 검증을 요청합니다.
2. **공개키 요청**
   * 송신 VASP의 Enclave 서버에서 VerifyVASP Central Server 를 통해 수신 VASP의 공개키를 요청합니다.
3. **공개키 제공**
   * 수신 VASP의 Enclave 서버는 공개키를 자체 데이터베이스에서 조회하고, 해당 키가 없을 경우 새로운 공개키를 생성하여 저장한 뒤 이를 송신 VASP에 전달합니다.
4. **개인정보 암호화**
   * 공개키 수신 후, 송신 VASP의 Enclave 서버는 사용자의 개인정보를 암호화합니다.
   * 암호화된 데이터는 VerifyVASP 중앙 서버를 통해 수신 VASP의 Enclave 로 전달됩니다.
5. **수취 VASP의 복호화 및 검증**
   * 수신 VASP의 Enclave 는 자신의 개인키를 이용해 전달 받은 데이터를 복호화하고, 수신인 정보를 검증합니다. 그 후, 수신인의 정보를 송신 VASP의 공개키로 다시 암호화하여 검증 결과와 함께 응답을 생성합니다.
6. **암호화된 데이터 반환**
   * 수신 VASP는 암호화된 수신인 개인정보와 검증 결과를 VerifyVASP Central Server 를 통해 송신 VASP의 Enclave 로 전송합니다.
7. **최종 복호화 및 저장**
   * 송신 VASP의 Enclave 는 자신의 개인키를 사용해 해당 데이터를 복호화하며, 복호화된 수신인 정보는 Enclave 전용 데이터베이스에 안전하게 저장됩니다.
   * 이러한 절차를 통해 VASP 간의 민감한 개인정보가 제3자 노출 없이 안전하게 교환될 수 있으며, 데이터 무결성과 프라이버시가 효과적으로 보장됩니다.

### 키 관리 (Key Management)

암호화 및 복호화에 사용되는 공개키와 개인키는 Enclave 의 데이터베이스 내에서 자동으로 생성, 저장 및 관리됩니다. 이러한 자동화 덕분에 VASP는 종단 간 암호화(E2EE)를 직접 처리할 필요가 없어, 구현이 훨씬 간편해집니다.

### 추가 구성 옵션 (Additional Options)

Enclave 서버는 암호화 과정을 완전히 자동으로 관리하지만, 유연성과 제어력을 높이기 위한 구성 옵션도 제공됩니다. 종단 간 암호화(E2EE)에 대한 개념을 이해하면, 이 과정을 더욱 효율적으로 구성하고 최적화할 수 있습니다.

이는 민감한 사용자 정보의 프라이버시와 무결성을 유지하면서, 안전한 데이터 교환을 가능하게 합니다.

<Accordion title="Configurable Key Options for End-to-End Encryption">
  ## 공개키 캐싱 (Public Key Caching)

  공개키 조회 효율성을 높이기 위해, Enclave 서버는 상대 VASP의 공개키를 일정 시간 동안 캐싱합니다. 이를 통해 동일한 키에 대한 반복 요청을 줄여 성능을 향상시킬 수 있습니다.

  * **Enclave 환경 변수 (Enclave Environment Variable)**
    * 공개키 캐싱 시간은 다음 환경 변수로 설정할 수 있습니다:
  * *VEGA\_PUBLIC\_KEY\_TTL (단위: 밀리초)*\*
    * **기본값**: **`1800000`** 밀리초 (30분).
    * **최소값**: **`600000`** 밀리초 (10분)
      시스템 성능과 보안 수준의 균형을 고려해 이 값을 조정할 수 있습니다.

  ## 공개키 유형 (Public Key Types)

  Enclave 는 종단 간 암호화(E2EE) 과정에서 유연성과 보안성을 강화하기 위해 여러 유형의 공개키 방식을 지원합니다. 검증 API 호출 시 요청 본문에 사용할 키 유형(keyType)을 지정할 수 있습니다.

  * `PerVasp`
    설명: VASP 단위로 하나의 키를 모든 검증에 공유
    * 장점: 캐싱 효율이 가장 높아 성능 최적화에 유리
    * 단점: 모든 검증 요청에 동일한 키가 사용되어 보안 수준이 낮음
  * `PerAddress`
    * 설명: 수취인 주소(address)마다 고유한 키를 생성
    * 장점: PerVasp보다 높은 보안성 확보 가능
    * 단점: 성능은 PerVasp 대비 약간 떨어짐
  * `PerVerification`
    * 설명: 검증 요청마다 새로운 키를 생성
    * 장점: 요청마다 고유한 키가 사용되어 가장 높은 보안성 제공
    * 단점: 캐싱이 불가능하므로 성능은 가장 낮음

  공개키 캐싱 시간과 키 유형을 적절히 선택함으로써, VASP 요구사항에 따라 보안성과 성능 사이의 최적 균형을 설정할 수 있습니다.
</Accordion>

<br />

## TravelRule 연동을 위한 VASP 작업 항목

TravelRule을 구현하기 위해 VASP 개발자가 따라야 할 주요 단계는 다음과 같습니다. 각 단계에 대한 상세 가이드는 후속 문서에 포함되어 있습니다.

1. **주요 시나리오 및 흐름 검토**

   구현을 시작하기 전에 핵심 사용 사례와 운영 흐름에 대해 충분히 이해합니다.
2. **VASP 아키텍처 기반 프로젝트 범위 정의**

   향후 확장성과 호환성을 확보할 수 있도록 TravelRule 아키텍처에 맞춰 구현 계획을 수립합니다.
3. **VerifyVASP 온보딩**

   VerifyVASP 회원가입을 통해 VASP를 등록하고 인증을 완료하여 연동에 필요한 자격 정보를 발급받습니다.
4. **VASP API 개발**

   거래 처리 및 검증 절차를 효과적으로 관리할 수 있도록 필요한 API를 구현합니다.
5. **Enclave 데이터베이스 구성**

   검증 및 규제 데이터를 안전하게 저장·관리할 수 있도록 전용 데이터베이스를 구성합니다.
6. **Enclave 배포**

   VerifyVASP Central Server 와 안전하게 연결할 수 있도록 Enclave Docker 환경을 설치 및 설정합니다.
7. **Robot VASP를 통한 테스트**

   Robot VASP를 활용해 종단 간 테스트를 통해 구현 상태를 검증하고 프로토콜 준수 여부를 확인합니다.
8. **지속적 유지관리 계획 수립**

   규제 변경이나 TravelRule 업데이트에 대응하기 위한 유지관리 전략을 마련합니다.

<br />

이 단계들을 따라 수행하면, VerifyVASP와의 보안적이고 규제에 부합하는 연동 환경을 안정적으로 구축할 수 있습니다.