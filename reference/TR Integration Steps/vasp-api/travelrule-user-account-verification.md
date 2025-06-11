---
title: Verify User Account API
api:
  file: TR_VASP_API_Spec.yaml
  operationId: travelrule-User-Account-Verification
hidden: false
---
ASP는 TravelRule 프로토콜 내에서 송신 VASP와 수신 VASP의 역할을 모두 수행해야 합니다. 이 API는 **수신 VASP** 역할을 위한 구현 요구사항입니다. 송신자가 지정한 수신 주소가 VASP의 소유 주소인지 여부를 검증하고 결과를 반환합니다.

***

## 구현 가이드

### 기능 요구사항

#### 1. VASP의 주소 소유 여부 검증

요청에 포함된 수신 주소(beneficiary.accountNumber)가 귀사 VASP로부터 발급된 주소인지 여부를 데이터베이스와 대조하여 확인하고, 결과를 응답해야 합니다.

#### 2. 검증 결과 응답

검증 결과를 `accountVerificationResult` 필드에 반환해야합니다. 검증 성공인 경우(VASP가 소유한 주소가 맞는 경우) `VERIFIED`, 검증 실패인 경우 `DENIED`로 반환합니다. 검증 결과가 `DENIED`인 경우, `reason` 필드에 아래의 사유 코드 중 하나를 반드시 포함해야 합니다:

<HTMLBlock>{`
<HTMLBlock>
<style>
  .custom-table {
    border-collapse: collapse;
    width: 100%;
    font-size: 14px;
  }

  .custom-table th,
  .custom-table td {
    border: 1px solid #ddd;
    padding: 12px;
    text-align: left;
    vertical-align: top;
  }

  .custom-table th {
    background-color: #f0f0f0;
    font-weight: 600;
  }

  .custom-table td {
    background-color: #ffffff;
  }

  .custom-table td.code-col {
    min-width: 250px;
    white-space: nowrap;
  }
</style>

<table class="custom-table">
  <thead>
    <tr>
      <th>사유 코드</th>
      <th>설명</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="code-col"><code>UNKNOWN-SYMBOL</code></td>
      <td>지원하지 않는 가상자산 종목 (예: 거래소에서 미지원인 종목)</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNKNOWN-NETWORK</code></td>
      <td>지원하지 않는 네트워크 (예: USDT-Ethereum 요청되었으나 거래소에서 USDT-Tron만 지원하는 경우)</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNKNOWN-ADDRESS</code></td>
      <td>확인할 수 없는 주소</td>
    </tr>
    <tr>
      <td class="code-col"><code>MISMATCHED-NAME</code></td>
      <td>수신자 이름 불일치</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNVERIFIED-KYC</code></td>
      <td>KYC 미완료 사용자</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNDEFINED-ERROR</code></td>
      <td>정의되지 않은 기타 오류</td>
    </tr>
  </tbody>
</table>
</HTMLBlock>
`}</HTMLBlock>

<br />

### 제약 사항

이 API는 1초 이내에 응답해야 합니다.

### 구현 권장사항

이 API는 주소 소유 여부만 검증하며, KYC/제재 조회 등 리스크 평가 로직은 포함하지 않는 것을 권장합니다.

### 환경 변수 설정

Enclave와의 정상 연동을 위해 아래와 같이 Enclave 환경 변수를 설정해야합니다.

* `VEGA_VERIFICATION_ACCOUNT_API_PATH`: 해당 API의 경로
* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: VerifyVASP 온보딩 시 발급받은 API Key

### 관련 링크

* [IVMS101 Guide](ref:ivms101-guide)

***

## API 명세