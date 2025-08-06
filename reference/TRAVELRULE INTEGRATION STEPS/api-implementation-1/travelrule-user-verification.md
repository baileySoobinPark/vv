---
title: Verify User API
api:
  file: TR_VASP_API_Spec.yaml
  operationId: travelrule-User-Verification
hidden: false
---
The **Verify User API** is called when a VASP acts as the **Beneficiary VASP** in the TravelRule protocol. It is invoked by the Beneficiary VASP’s Enclave upon receiving a request from the Ordering VASP.\
The Beneficiary VASP uses this API to verify the beneficiary’s information, confirm compliance with regulatory requirements, and determine whether to allow the asset transfer.

***

## Implementation Guide

### Functional Requirements

#### 1. Verify Personal Information

* Compare the beneficiary’s personal information, provided in IVMS101 format, against your VASP’s records.
* This includes name verification and address verification.

#### 2. Verify Travel Rule Regulatory Compliance

Check whether the transaction meets compliance requirements by verifying:

* KYC completion status
* AML policy compliance
* STR monitoring and sanction screening for the originator
* Any additional filtering as required by your VASP’s internal policies

#### 3. Return Requested Information from Ordering VASP

If the Ordering VASP specifies <code>requiredBeneficiaryInfo</code>, the requested fields must be returned in the <code>>ivms101</code> object.

* If the requested information is unavailable or cannot be provided:
  * Set verificationResult to <code>DENIED</code>
  * Set reason to <code>UNAVAILABLE-INFORMATION</code>
* Only return requested fields; unrequested fields must be returned as empty values.
* Return the wallet address exactly as provided in the request.
  * If the address is invalid, set <code>verificationResult</code> to <code>DENIED</code>.

#### 4. Return Verification Result

Return the final result in the <code>result</code> field:

* <code>VERIFIED</code> → No issues found; include beneficiary details in IVMS101 format.
* <code>DENIED</code> → Issues found; include one of the following failure reason codes in both <code>reason</code> and <code>message</code> fields:

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
      <td>Unsupported asset symbol<br>("Ethereum"</td>
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
* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: API 인증을 위한 인증 토큰 값
* `VEGA_VERIFICATION_AUTHORIZATION_KEY`: API 인증 토큰을 전달할 header key

### 관련 링크

* [IVMS101 Guide](ref:ivms101-guide)

***

## API 명세