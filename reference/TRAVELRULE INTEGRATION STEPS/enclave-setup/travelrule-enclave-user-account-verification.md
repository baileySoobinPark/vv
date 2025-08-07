---
title: Request User Account Verification API
api:
  file: TR_Enclave_API_Spec.yaml
  operationId: travelrule-Enclave-User-Account-Verification
hidden: false
---
This API is used by the sending VASP to request ownership verification of a specific wallet address from the receiving VASP. The response will confirm whether the provided address is owned by the receiving VASP.

***

## Verification Result

The verification result can be found in the `accountVerificationResult` field of the response. If the result is `DENIED`, the reason field will contain one of the following failure reasons.

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
      <td class="code-col"><code>BLACKLISTED</code></td>
      <td>수신 계정이 블랙리스트에 포함됨</td>
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
`}</HTMLBlock>

***

## API 명세