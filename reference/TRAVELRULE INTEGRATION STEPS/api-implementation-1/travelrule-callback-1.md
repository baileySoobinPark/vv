---
title: Callback API
api:
  file: TR_VASP_API_KR_Spec.yaml
  operationId: travelrule-callback
hidden: false
---
VASP는 TravelRule 프로토콜 내에서 송신 VASP와 수신 VASP의 역할을 모두 수행해야 합니다. 이 API는 두 역할 모두에서 비동기적 콜백 상황을 처리하기 위한 공통 인터페이스입니다. Enclave는 상대 VASP로부터 Report API가 호출되었을 때 이 API를 실행합니다.

***

## 구현 가이드

### 기능 요구사항

#### 1. 콜백 타입 분기 처리

요청의 `callbackType` 필드에 따라 각 콜백 유형에 맞는 비즈니스 로직으로 분기 처리해야 합니다. 지원되는 콜백 유형은 아래와 같으며, `VERIFICATION_RESULT`, `TX_REPORT`, `ERROR_REPORT`는 필수 구현 대상입니다.

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
      <th><code>callbackType</code></th>
      <th>설명</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="code-col"><code>VERIFICATION_RESULT</code></td>
      <td>송신 VASP 역할에서 사용됩니다. 수신자 검증이 종료되어 결과가 비동기적으로 전달될 때 호출됩니다.</td>
    </tr>
    <tr>
      <td class="code-col"><code>TX_REPORT</code></td>
      <td>수신 VASP 역할에서 사용됩니다. 송신 VASP가 트랜잭션 결과를 Report할 때 호출됩니다.</td>
    </tr>
    <tr>
      <td class="code-col"><code>ERROR_REPORT</code></td>
      <td>수신 VASP 역할에서 사용됩니다. 송신 VASP가 오류를 Report할 때 호출됩니다.</td>
    </tr>
    <tr>
      <td class="code-col"><code>CHAINALYSIS_KYT_RESULT</code></td>
      <td>Chainalysis KYT 결과를 비동기 방식으로 전달할 때 호출됩니다. 선택적으로 구현 가능합니다.</td>
    </tr>
    <tr>
      <td class="code-col"><code>REFINITIV_WCO_RESULT</code></td>
      <td>Refinitiv WCO 결과를 비동기 방식으로 전달할 때 호출됩니다. 선택적으로 구현 가능합니다.</td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

#### 2. VERIFICATION\_RESULT 처리

콜백으로 수신한 검증 결과에 따라 후속 조치를 수행해야 합니다.

* 검증 성공 시, 이어서 송신 VASP측 수신자 검증을 진행하거나 트랜잭션을 실행합니다.
* 검증 실패 시, 자산의 출금을 취소로 처리하고 사용자에게 적절한 안내 메세지와 함께 전송 실패를 고지합니다. `data.reason`필드로부터 실패 사유를 참조하여 안내 메세지에 반영할 수 있습니다.

`VERIFICATION_RESULT` 유형 콜백 메시지 예시는 아래와 같습니다.

```json 'VERIFIED' 결과 메시지 예시
{
   "callbackType":"VERIFICATION_RESULT",
   "data":{
      "verificationUuid":"64ab871b-14a3-47df-9b80-368e29fe8180",
      "verificationResult":"VERIFIED",
      "reason":"OK",
      "message":"",
      "ivms101":{
         "beneficiary":{
            "beneficiaryPersons":[
               {
                  "naturalPerson":{
                     "name":{
                        "nameIdentifier":[
                           {
                              "primaryIdentifier":"James",
                              "nameIdentifierType":"LEGL"
                           }
                        ],
                        "localNameIdentifier":[
                           {
                              "primaryIdentifier":"김재원",
                              "nameIdentifierType":"LEGL"
                           }
                        ]
                     },
                     "geographicAddress":[
                        {
                           "addressType":"GEOG",
                           "townName":"Yeoksam-dong",
                           "addressLine":[
                              "14 Teheran-ro 4-gil, Gangnam-gu",
                              "4th floor"
                           ],
                           "country":"KR"
                        }
                     ],
                     "nationalIdentification":{
                        "nationalIdentifier":"12345-67890",
                        "nationalIdentifierType":"IDCD"
                     },
                     "customerIdentification":"1234569999",
                     "dateAndPlaceOfBirth":{
                        "dateOfBirth":"1985-03-14",
                        "placeOfBirth":"Nonsan"
                     },
                     "countryOfResidence":"KR"
                  }
               }
            ],
            "accountNumber":[
               "0xb0bFf9721871e22653358956cf59a5FdBF3D752F"
            ]
         }
      }
   }
}
```
```Text DENIED 결과 메시지 예시
{
   "callbackType":"VERIFICATION_RESULT",
   "data":{
      "verificationUuid":"64ab871b-14a3-47df-9b80-368e29fe8180",
      "verificationResult":"DENIED",
      "reason":"UNKNOWN-ADDRESS",
      "ivms101":{
         "originator":{
            "originatorPersons":[
               {
                  "naturalPerson":{
                     "name":{
                        "nameIdentifier":[
                           {
                              "primaryIdentifier":"James",
                              "secondaryIdentifier":"Din",
                              "nameIdentifierType":"LEGL"
                           }
                        ]
                     }
                  }
               }
            ],
            "accountNumber":[
               "0x5811001506550d8356a215be229c15b6ef371a9a"
            ]
         },
         "beneficiary":{
            "beneficiaryPersons":[
               {
                  "naturalPerson":{
                     "name":{
                        "nameIdentifier":[
                           {
                              "primaryIdentifier":"Taylor",
                              "secondaryIdentifier":"Robbins",
                              "nameIdentifierType":"LEGL"
                           }
                        ]
                     }
                  }
               }
            ],
            "accountNumber":[
               "0xb0bFf9721871e22653358956cf59a5FdBF3D752F"
            ]
         },
         "originatingVASP":{
            "legalPerson":{
               "name":{
                  "nameIdentifier":[
                     {
                        "legalPersonName":"Lambda256",
                        "legalPersonNameIdentifierType":"LEGL"
                     }
                  ]
               },
               "geographicAddress":[
                  {
                     "addressType":"GEOG",
                     "postcode":"123123c",
                     "townName":"Yeoksam-dong",
                     "addressLine":[
                        "sechogu street",
                        "100-100"
                     ],
                     "country":"KR"
                  }
               ],
               "nationalIdentification":{
                  "nationalIdentifier":"KR0001",
                  "nationalIdentifierType":"RAID",
                  "registrationAuthority":"RA000151"
               }
            }
         },
         "beneficiaryVASP":{
            "legalPerson":{
               "name":{
                  "nameIdentifier":[
                     {
                        "legalPersonName":"Lambda256 Exchange",
                        "legalPersonNameIdentifierType":"LEGL"
                     }
                  ]
               },
               "geographicAddress":[
                  {
                     "addressType":"GEOG",
                     "postcode":"234234",
                     "townName":"Yeoksam-dong",
                     "addressLine":[
                        "sechogu street",
                        "100-100"
                     ],
                     "country":"KR"
                  }
               ],
               "nationalIdentification":{
                  "nationalIdentifier":"123456",
                  "nationalIdentifierType":"RAID",
                  "registrationAuthority":"RA000677"
               }
            }
         }
      }
   }
}
```

<br />

`data.result`가 DENIED 또는 ERROR인 경우 전달될 수 있는 실패 사유 코드는 아래와 같습니다.

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
    min-width: 150px;
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
    <tr>
      <td class="code-col"><code>BENEFICIARY-ACCOUNT-NOT-MATCHED</code></td>
      <td>-</td>
      <td>수신 VASP가 송신 VASP가 전송한 주소를 변경하여 반환한 경우</td>
    </tr>
    <tr>
      <td class="code-col"><code>REQUEST-TIMEOUT</code></td>
      <td>-</td>
      <td>검증 요청 후 대기시간이 초과한 경우</td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

#### 3. TX\_REPORT 처리 (수신 VASP 역할)

콜백으로 수신한 트랜잭션 Hash가 수신자의 실제 입금 주소로 발생한 트랜잭션인지 확인하고 결과 및 이력을 데이터베이스에 기록합니다.

#### 4. ERROR\_REPORT 처리 (수신 VASP 역할)

오류 보고 내용을 확인한 뒤 해당 전송을 취소한 뒤, 트랜잭션 추적을 중단하고 로그 기록을 남깁니다.

#### 5. CHAINALYSIS\_KYT\_RESULT 처리 (선택사항)

콜백으로 수신한 Chainalysis KYT 리스크 평가 결과에 따라 다음과 같은 작업을 수행할 수 있습니다.

* Originator 또는 Beneficiary의 평가 데이터 갱신
* 트랜잭션 허용 또는 차단 결정

#### 6. REFINITIV\_WCO\_RESULT 처리 (선택사항)

콜백으로 수신한 Refinitiv WCO 리스크 평가 결과에 따라 다음과 같은 작업을 수행할 수 있습니다.

* Originator 또는 Beneficiary의 평가 데이터 갱신
* 트랜잭션 허용, 재개 또는 차단 결정

<br />

### 제약 조건

* 이 API는 1초 이내에 응답해야 합니다.
* 응답의 HTTP 상태 코드는 200 OK만 허용됩니다.
* 동일한 콜백 요청이 여러 번 수신되어도 처리 결과가 동일하도록 멱등성을 보장해야 합니다.\
  (ex) 중복 요청 시 내부 처리 로직에서 무시하도록 구현

<br />

### 구현 권장사항

* 콜백 API의 경우 응답 속도가 중요하므로, 시간 소모가 큰 작업은 응답 이후 비동기 방식으로 처리하는 것을 권장합니다.

<br />

### Enclave 연동 설정

Enclave와의 정상 연동을 위해 아래 환경 변수를 설정해야 합니다.

* `VEGA_VERIFICATION_CALLBACK_API_PATH`: 해당 API 경로
* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: VerifyVASP 온보딩 시 발급받은 API Key

***

## API 명세