---
title: VerifyName API
api:
  file: VN_VASP_API_KR_Spec.yaml
  operationId: verifyName-Request-Verification
hidden: false
---
VerifyName 프로토콜 검증 과정에서 Enclave에 의해 호출되는 API입니다. 전송 자산 정보와 수신 주소의 유효성을 검증하고, 해당 주소 소유자의 성명과 생년월일을 응답으로 반환합니다. 반환된 개인 정보는 Enclave 내부에서 송신자 정보와의 비교 검증에 사용됩니다. VerifyName을 지원하는 모든 VASP는 반드시 이 API를 구현하여 타 VASP들로부터의 소유자 검증 요청에 대응해야 합니다.

***

## 구현 가이드

### 기능 요구사항

#### 1. 시나리오별 비즈니스 로직 구현

VerifyName 프로토콜은 트랜잭션 실행 시점을 기준 사전 검증과 사후 검증을 모두 지원합니다. VASP는 시나리오에 따라 적절한 검증 로직을 수행하고 사용자 정보를 응답에 반환하여 Enclave로 전달해야 합니다.

**사후 검증(Post-Verification) 구현 요구사항**

* 요청 type이 `VerifyOriginator`인 경우로, 귀사의 VASP는 송신 VASP로서 검증 요청에 대응해야 합니다. 송신 VASP로부터 자산 전송 트랜잭션이 먼저 실행되어, 수신 VASP가 검증을 요청한 경우입니다.
* 요청에 포함된 tx\_hash 값이 귀사의 VASP가 실행한 트랜잭션이 맞는지 검증하고, 결과를 응답의 `verification_results` 객체 내 `tx_hash` 필드로 반환해야 합니다.
* 해당 트랜잭션의 수신 주소가 API 요청에 포함된 `supplementary_data.envelope.address`의 주소와 일치하는지 검증하고, 결과를 응답의 `verification_results` 객체 내 `address` 필드로 반환해야 합니다.
* 주소가 일치하는 경우, 해당 주소의 소유주 정보를 `debtor` 객체에 포함하여 반환합니다.

**사전 검증(Pre-Verification) 구현 요구사항**

* 요청 type이 `VerifyBeneficiary`인 경우로, 귀사의 VASP는 수신 VASP로서 검증 요청에 대응해야 합니다. 송신 VASP가 자산 전송을 실행하기에 앞서 수신 VASP로 검증을 요청한 경우입니다.
* 요청의 `supplementary_data.envelope.address`의 주소가 귀사의 VASP에 등록된 수취인 주소 중 하나와 일치하는지 확인하고, 결과를 응답의 `verification_results` 객체 내 `address` 필드로 반환해야 합니다.
* 일치하는 주소가 존재하는 경우, 해당 주소의 소유주 정보를 `crditor` 객체에 포함하여 반환합니다.

#### 2. 검증 수행 결과 반환

항목별 검증 수행 결과를 응답의 `verification_results` 객체 내 관련 필드에 명시하여 반환해야 합니다. 각 필드는 `MATCHED`, `MISMATCHED`, `SKIPPED` 중 하나의 값을 가질수 있으며, **검증을 수행하지 않은 항목의 경우(아래 표의 필수 검증 여부가 Optional인 경우)에도 빈 값으로 반환 또는 키를 제외하지 않고`SKIPPED`로 반드시 포함하여 반환**합니다. 각 항목별 결과는 다음과 같은 정책에 따라 결정할 수 있습니다.

<HTMLBlock>{`
<table class="verify-params">
  <thead>
    <tr>
      <th>필드 명</th>
      <th>검증 필수여부</th>
      <th>결과 반환 정책</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>ticker</code></td>
      <td>Required</td>
      <td>귀사 VASP가 해당 자산을 지원하는 경우 <code>MATCHED</code>, 그렇지 않으면 <code>MISMATCHED</code></td>
    </tr>
    <tr>
      <td><code>network</code></td>
      <td>Optional</td>
      <td>귀사의 VASP가 해당 네트워크를 지원하면 <code>MATCHED</code>, 그렇지 않으면 <code>MISMATCHED</code><br>검증 미요청 항목이거나 값이 제공되지 않은 경우 <code>SKIPPED</code></td>
    </tr>
    <tr>
      <td><code>address</code></td>
      <td>Required</td>
      <td>Pre-verification의 경우, 요청 address가 등록된 수취인 주소인지 확인<br>Post-verification의 경우, 해당 트랜잭션에서 사용된 수취인 주소와 비교<br>일치하면 <code>MATCHED</code>, 다르면 <code>MISMATCHED</code></td>
    </tr>
    <tr>
      <td><code>tag</code></td>
      <td>Optional</td>
      <td>요청 tag 값이 등록된 tag와 일치하면 <code>MATCHED</code>, 다르면 <code>MISMATCHED</code><br>검증 미요청 항목이거나 값이 제공되지 않은 경우 <code>SKIPPED</code></td>
    </tr>
    <tr>
      <td><code>tx_hash</code></td>
      <td>Optional</td>
      <td>요청 tx_hash 값이 귀사의 VASP가 실행한 트랜잭션인지 확인하여<br>일치하면 <code>MATCHED</code>, 다르면 <code>MISMATCHED</code>, 검증 미요청 항목이거나 값이 제공되지 않은 경우 <code>SKIPPED</code></td>
    </tr>
    <tr>
      <td><code>dti</code></td>
      <td>Optional</td>
      <td>해당 디지털 자산 식별자(DTI)를 지원하면 <code>MATCHED</code>, 아니면 <code>MISMATCHED</code>, 검증 미요청 항목이거나 값이 제공되지 않은 경우 <code>SKIPPED</code></td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

#### 3. 주소 소유주 정보 반환

`address` 또는 `tx_hash` 필드의 검증 결과가 `MATCHED`인 경우, 관련 계정의 소유주 정보를 응답에 포함하여 반환해야 합니다. 정보 제공 범위는 다음과 같습니다.

<HTMLBlock>{`
ㅇ<style>
.personal-info-table {
  width: 100%;
  border-collapse: collapse;
  background-color: white;
  font-size: 14px;
}

.personal-info-table th,
.personal-info-table td {
  border: 1px solid #ccc;
  padding: 10px;
  text-align: left;
  vertical-align: top;
}

.personal-info-table thead {
  background-color: white;
  font-weight: bold;
}  
</style>
<table class="personal-info-table">
  <thead>
    <tr>
      <th>계정 유형</th>
      <th>개인정보 제공 범위</th>
      <th>필수 여부</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="2">개인</td>
      <td>이름</td>
      <td>필수</td>
    </tr>
    <tr>
      <td>생년월일<small>(ex)<code>2025-01-01</code></small></td>
      <td>필수</td>
    </tr>
    <tr>
      <td rowspan="5">법인</td>
      <td>이름</td>
      <td>필수</td>
    </tr>
    <tr>
      <td>법인 설립일 <small>(ex)<code>2025-01-01</code></small></td>
      <td>필수</td>
    </tr>
    <tr>
      <td>LEI</td>
      <td>선택</td>
    </tr>
    <tr>
      <td>BIC</td>
      <td>선택</td>
    </tr>
    <tr>
      <td>Identification &amp; Issuer</td>
      <td>선택</td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

검증 요청 `type` 에 따른 소유주 정보 응답 예시는 다음과 같습니다.

* `VerifyOriginator` 인 경우 트랜잭션 송신 계좌 소유주 정보를 `debtor`객체로 반환합니다.
  <HTMLBlock>{`
  <style>
    .custom-accordion {
      border: 1px solid #d0d7de;
      border-radius: 8px;
      margin-bottom: 12px;
      overflow: hidden;
      transition: border 0.3s ease;
    }

    .custom-accordion[open] {
      border: 2px solid #1d78ff;
    }

    .custom-accordion summary {
      padding: 12px 16px;
      cursor: pointer;
      list-style: none;
      font-weight: 500;
      background-color: #f9f9f9;
    }

    .custom-accordion summary::marker,
    .custom-accordion summary::-webkit-details-marker {
      display: none;
    }

    .custom-accordion summary::before {
      content: "›";
      display: inline-block;
      margin-right: 8px;
      transform: rotate(0deg);
      transition: transform 0.2s ease;
    }

    .custom-accordion[open] summary::before {
      transform: rotate(90deg);
    }

    .custom-accordion pre {
      background-color: #ffffff;
      padding: 16px;
      margin: 0;
      font-size: 14px;
      overflow-x: auto;
    }
  </style>
  <details class="custom-accordion">
    <summary>Example of Response Body: <code>VerifyOriginator</code> 타입, 개인 계정인 경우</summary>

    <pre><code class="language-json">
  {
  	"verification_results": {
  	  "ticker": "MATCHED",        
  	  "network": "MISMATCHED",    
  	  "address": "SKIPPED",       
  	  "tag": "SKIPPED",
  	  "tx_hash": "SKIPPED",      
  	  "dti": "SKIPPED",         
  	},
  	"debtor": {
  		"name": "HONG KIL DONG",
  		"supplementary_data": {
  			"envelope": {
  				"name": {                  
  					"first_name": "GIL DONG",
  					"last_name": "HONG",
  				},
  			}
  		},
  		"identification": {
  			"private_identification": {
  				"date_and_place_of_birth": {
  					"birth_date": "2025-01-01",
  				}
  			}
  		}
  	}
  }
    </code></pre>
  </details>
  <details class="custom-accordion">
    <summary>Example of Response Body: <code>VerifyOriginator</code> 타입, 법인 계정인 경우</summary>

    <pre><code class="language-json">
  {
  	"verification_results": {
  	  "ticker": "MATCHED",        
  	  "network": "MISMATCHED",    
  	  "address": "SKIPPED",       
  	  "tag": "SKIPPED",
  	  "tx_hash": "SKIPPED",      
  	  "dti": "SKIPPED",         
  	},
  	"debtor": {
  		"name": "HONG KIL DONG",
  		"supplementary_data": {
  			"envelope": {
  				"name": {                  
  					"first_name": "GIL DONG",
  					"last_name": "HONG",
  				},
  			}
  		},
  		"identification": {
  			"organisation_identification": {
  				"supplementary_data": {
  					"envelope": {
  						"date_of_incorporation": "2020-01-01",
  					}
  				},
  			},
  			"lei": "506700GE1G29325QX363",
  			"bic": "KRKRKR"
  			"other": {
  				"identification": "5493001KJTIIGC8Y1R12",
  				"issuer": "ISO17442",
  			},
  		}
  	}
  }
    </code></pre>
  </details>
  `}</HTMLBlock>
* `VerifyBeneficiary` 인 경우 수신 계좌 소유주 정보를 `creditor`객체로 반환합니다.
  <HTMLBlock>{`
  <details class="custom-accordion">
    <summary>Example of Response Body: <code>VerifyBeneficiary</code> 타입, 개인 계정인 경우</summary>

    <pre><code class="language-json">
  {
  	"verification_results": {
  	  "ticker": "MATCHED",        
  	  "network": "MISMATCHED",    
  	  "address": "SKIPPED",       
  	  "tag": "SKIPPED",
  	  "tx_hash": "SKIPPED",      
  	  "dti": "SKIPPED",         
  	},
  	"creditor": {
  		"name": "HONG KIL DONG",
  		"supplementary_data": {
  			"envelope": {
  				"name": {                  
  					"first_name": "GIL DONG",
  					"last_name": "HONG",
  				},
  			}
  		},
  		"identification": {
  			"private_identification": {
  				"date_and_place_of_birth": {
  					"birth_date": "2025-01-01",
  				}
  			}
  		}
  	}
  }
    </code></pre>
  </details>
  <details class="custom-accordion">
    <summary>Example of Response Body: <code>VerifyBeneficiary</code> 타입, 법인 계정인 경우</summary>

    <pre><code class="language-json">
  {
  	"verification_results": {
  	  "ticker": "MATCHED",        
  	  "network": "MISMATCHED",    
  	  "address": "SKIPPED",       
  	  "tag": "SKIPPED",
  	  "tx_hash": "SKIPPED",      
  	  "dti": "SKIPPED",         
  	},
  	"creditor": {
  		"name": "HONG KIL DONG",
  		"supplementary_data": {
  			"envelope": {
  				"name": {                  
  					"first_name": "GIL DONG",
  					"last_name": "HONG",
  				},
  			}
  		},
  		"identification": {
  			"organisation_identification": {
  				"supplementary_data": {
  					"envelope": {
  						"date_of_incorporation": "2020-01-01",
  					}
  				},
  			},
  			"lei": "506700GE1G29325QX363",
  			"bic": "KRKRKR"
  			"other": {
  				"identification": "5493001KJTIIGC8Y1R12",
  				"issuer": "ISO17442",
  			},
  		}
  	}
  }
    </code></pre>
  </details>
  `}</HTMLBlock>