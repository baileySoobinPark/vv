---
title: Verify User API
api:
  file: TR_VASP_API_Spec.yaml
  operationId: travelrule-User-Verification
hidden: false
---
VASP는 TravelRule 프로토콜 상에서 송신 VASP와 수신 VASP의 역할을 모두 수행해야 합니다. 본 API는 수신 VASP 역할에 해당하는 구현 요구사항으로, 송신 VASP의 요청에 따라 Enclave가 호출합니다. 수신VASP는 이 API를 통해 수신인 정보와 규제 요건 충족 여부를 검증하여 그 결과에 따라 자산 이전을 허가할 수 있습니다.

***

## 구현 가이드

### 기능 요구사항

#### 1. 개인 정보 검증

IVMS101 포맷으로 전달된 수신자 개인정보를 귀사 VASP의 보유 정보와 대조하여 일치 여부를 검증해야합니다. 이름 검증 및 주소 검증을 포함합니다.

#### 2. Travel Rule 규제 요건 검증

아래 항목들을 검토하여 해당 자산 전송의 규제 준수 요건 충족 여부 검증해야 합니다.

* KYC 완료 여부
* AML 정책 충족 여부
* 송신자에 대한 STR 모니터링 및 Sanction Screening 검증
* VASP 정책에 따라 추가적인 필터링 수행

#### 3. 송신 VASP의 요청 정보 반환

송신 VASP가 `requiredBeneficiaryInfo`에 지정한 요청 항목를 `ivms101` 객체에 채워 반드시 전달해야 합니다.

* 요청된 정보를 보유하지 않았거나 제공할 수 없는 경우, `verificationResult`는 `DENIED`, `reason`은 `UNAVAILABLE-INFORMATION`으로 설정해야 합니다.
* 요청된 항목만 반환해야 하며, 요청되지 않은 항목은 반드시 빈 값으로 반환합니다.
* 지갑 주소는 요청 원본 그대로 반환하며, 유효하지 않은 주소인 경우 `verificationResult`를 `DENIED`로 반환합니다.

#### 4. 검증 결과 응답

최종 검증 결과를 `result`필드로 반환해야 합니다. 검증 결과 해당 전송건에 문제가 없다고 판단하는 경우 `VERIFIED`로 응답하고, 수신자 정보를 IVMS101으로 함께 반환합니다. 정보를 검증할 수 없거나 검증 결과에 문제가 있는 경우 결과를 `DENIED`로 응답하고, 아래 실패 사유 코드 중 하나를 선택하여 `reason`필드와 `message` 필드에 값을 반환해야 합니다.

<br />

<HTMLBlock>{`
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
    min-width: 200px;
    white-space: nowrap;
  }
</style>

<table class="custom-table">
  <thead>
    <tr>
      <th><code>reason</code></th>
      <th><code>message</code></th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="code-col"><code>UNKNOWN-SYMBOL</code></td>
      <td>미지원 자산 심볼<br>(ex)"ETH"</td>
      <td>지원하지 않는 가상자산 종목 (예: 거래소에서 미지원인 종목)</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNKNOWN-NETWORK</code></td>
      <td>미지원 네트워크 이름<br>(ex)"Ethereum"</td>
      <td>지원하지 않는 네트워크 (예: USDT-Ethereum 요청되었으나 거래소에서 USDT-Tron만 지원하는 경우)</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNKNOWN-ADDRESS</code></td>
      <td>대상 주소<br>(ex)"0xasd..."</td>
      <td>확인할 수 없는 지갑 주소</td>
    </tr>
    <tr>
      <td class="code-col"><code>LACK-OF-INFORMATION</code></td>
      <td>콤마(,)로 구분된 누락 필드 목록<br>(ex)"ACCOUNT_NUMBER"</td>
      <td>송신자 정보 부족</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNAVAILABLE-INFORMATION</code></td>
      <td>콤마(,)로 구분된 제공 불가 필드 목록<br>(ex)"ACCOUNT_NUMBER"</td>
      <td>제공 불가한 수신자 정보</td>
    </tr>
    <tr>
      <td class="code-col"><code>BLACKLISTED</code></td>
      <td>대상 주소<br>(ex)"0xasd..."</td>
      <td>제재 목록에 포함된 주소</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNVERIFIED-KYC</code></td>
      <td>-</td>
      <td>KYC 미완료</td>
    </tr>
    <tr>
      <td class="code-col"><code>MISMATCHED-NAME</code></td>
      <td>-</td>
      <td>수신자 이름 불일치</td>
    </tr>
    <tr>
      <td class="code-col"><code>NOT-ALLOWED</code></td>
      <td>해당 사유<br>(ex) "This user is locked by internal policy."</td>
      <td>내부 정책으로 인해 거부됨</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNDEFINED-ERROR</code></td>
      <td>-</td>
      <td>정의되지 않은 기타 오류</td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

### 제약 조건

이 API는 5초 이내에 응답해야 합니다.

### 구현 권장사항

* 규제 요건과 관계없이 모든 가상자산 전송건에 대해 Travel Rule 프로토콜을 적용하는 것을 권장합니다. 보안성과 컴플라이언스를 강화하고 운영 리소스를 줄일 수 있습니다.
* 송신 VASP의 요청의 `isExceedingThreshold` 필드가 `false`로 설정된 경우 해당 전송은 Travel Rule 적용 대상이 아니며, 사용자 검증 절차 없이 전송할 수 있습니다.

### Enclave 연동 설정

Enclave와의 정상 연동을 위해 아래와 같이 Enclave 환경 변수를 설정해야합니다.

* `VEGA_VERIFICATION_API_PATH`: 해당 API 경로
* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: VerifyVASP 온보딩 시 발급받은 API Key

### 관련 링크

* [IVMS101 Guide](ref:ivms101-guide)

***

## API 명세