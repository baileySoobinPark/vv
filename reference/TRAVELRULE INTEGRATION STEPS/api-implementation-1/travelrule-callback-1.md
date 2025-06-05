---
title: Callback API
excerpt: >
  The Callback API serves as a common interface for handling various
  asynchronous flows. Depending on the callbackType, it may be invoked when your
  VASP acts as either the Originating VASP or the Beneficiary VASP. Your
  implementation must function correctly in all possible cases. The Enclave will
  invoke this API when a counterparty VASP triggers a Report APIs.


  ### Functional Requirements

  ** 1. Callback Type Support**

  The implementation must route the request to appropriate business logic for
  each callbackType values. The following are the possible values for the
  callbackType field and the scenarios in which they are used:
    - **VERIFICATION_RESULT**: callbackType for Originating VASP role. This callback is triggered by Enclave when it the user verification process is terminated and result is delivered to the Enclave asynchronously.
    - **TX_REPORT**: callbackType for Beneficiary VASP role. This callback is triggered by Enclave when the Originating VASP sends a transaction result report.
    - **ERROR_REPORT**: callbackType for Beneficiary VASP role. This callback is triggered by Enclave when the Originating VASP sends an error report.
  `VERIFICATION_RESULT`, `TX_REPORT`, and `ERROR_REPORT` types must be
  implemented as mandatory processing logic.
    - **CHAINALYSIS_KYT_RESULT**: This callback is triggered by Enclave when a risk assessment result is delivered from the Chainalysis KYT request.
    - **REFINITIV_WCO_RESULT**: This callback is triggered by Enclave when a risk assessment result is delivered from the Refinitiv WCO request.
  `CHAINALYSIS_KYT_RESULT` and `REFINITIV_WCO_RESULT` types are optional and can
  be implemented only if the corresponding screening APIs are in use.


  ** 2. VERIFICATION_RESULT Callback Type Handling (as Originating VASP)**

  When a verification result is received, your VASP must perform further
  required actions in accordance with the verification result. For example,
    - For successful verification, proceed with either initiating beneficiary verification on the Originating VASP side or executing the on-chain transaction.
    - For failed verification, cancel the associated asset transfer and notify the originator user of the failure with proper message. data.reason field can be refered to generate the failure messages.

  <details>
    <summary>Example of Request Body for `VERIFICATION_RESULT` callback type with `VERIFIED` result</summary>

    ``` json
      {
        "callbackType": "VERIFICATION_RESULT",
        "data": {
          "result": "VERIFIED",
          "verificationUuid": "ecb457e3-2307-4e72-8a42-16a3774e154b"
        }
      }
    ```
  </details>


  <details>
    <summary>Example of Request Body for `VERIFICATION_RESULT` callback type with `DENIED` result</summary>

    ``` json
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
  </details>


  When data.result is `DENIED` or `ERROR`, following data.reason and
  data.message can be returned.


  | Reason                             | Result | Message
  Example                                              | Description |

  |------------------------------------|--------|--------------------------------------------------------------|-------------|

  | `UNKNOWN-SYMBOL`                   | DENIED |
  `"ETH"`                                                      | Error code
  returned when the virtual asset symbol entered by the Originating VASP is not
  supported by the Beneficiary VASP. |

  | `UNKNOWN-NETWORK`                  | DENIED |
  `"Ethereum"`                                                 | Error code
  returned when the network entered by the Originating VASP is either not
  supported by the Beneficiary VASP or contains insufficient network
  information. This error is also returned if the asset symbol matches but is on
  a different network (e.g., USDT on Ethereum vs supported only on Tron). |

  | `UNKNOWN-ADDRESS`                  | DENIED | `"0x... is not
  registered."`                                 | Error code returned when the
  wallet address entered by the Originating VASP does not belong to a wallet
  managed by the Beneficiary VASP. |

  | `LACK-OF-INFORMATION`              | DENIED |
  `"ACCOUNT_NUMBER"`                                           | A
  comma-separated list of required but missing personal information fields.
  Error occurs when Originator info is insufficient for Beneficiary VASP to
  perform verification. |

  | `UNAVAILABLE-INFORMATION`         | DENIED |
  `"ACCOUNT_NUMBER"`                                           | A
  comma-separated list of undeliverable personal information fields. Error
  occurs when the Beneficiary VASP cannot provide certain information requested
  by the Originating VASP. |

  | `BLACKLISTED`                      | DENIED | `"0x.. is listed on the
  blacklist."`                         | Error code returned when the Sanction
  Screening results for the Originator indicate a compliance issue. |

  | `UNVERIFIED-KYC`                   | DENIED | `"0x.. is unverified
  KYC"`                                   | Error code returned when the
  Beneficiary has not completed the KYC verification process with the
  Beneficiary VASP. |

  | `MISMATCHED-NAME`                  | DENIED | `"Name is not
  matched."`                                     | Error code returned when the
  Beneficiary's name does not match the name provided by the Originating VASP. |

  | `NOT-ALLOWED`                      | DENIED | `"This user is locked by
  internal policy."`                  | Error code returned when the Beneficiary
  VASP rejects the user verification due to internal policies. |

  | `UNDEFINED-ERROR`                  | DENIED | `"Undefined Error is
  occurred."`                             | Error code returned when an
  unspecified or unknown error occurs. |

  | `BENEFICIARY-ACCOUNT-NOT-MATCHED` | ERROR  | `"Beneficiary account is not
  matched with requested account."` | Error code returned when the Beneficiary
  VASP modifies the beneficiary address from the one that was requested. |

  | `REQUEST-TIMEOUT`                  | ERROR  | `"Request
  timeout."`                                         | Error code returned when
  the requested verification process exceeds the allowed timeout period. |


  ** 3. TX_REPORT Callback Handling (as Beneficiary VASP)**

  When a transaction report is received, your VASP can
    - Match the reported on-chain transaction hash with the actual deposit to the beneficiary address.
    - Confirm that the asset transfer has been requested and log the details.

  <details>
    <summary>Example of Request Body for TX_REPORT callback type</summary>

    ``` json
      {
        "callbackType":"TX_REPORT",
        "data":{
            "verificationUuid":"64ab871b-14a3-47df-9b80-368e29fe8181",
            "txHash":"8a54d58ca4100112a5430818776d74898f2232770bae03046862575cb851a042",
            "vout":"2"
        }
      }
    ```
  </details>


  ** 4. ERROR_REPORT Callback Handling (as Beneficiary VASP)**

  When an error report is received, your VASP can
    - Cancel the associated asset transfer request.
    - Stop tracking the transaction and log the error for auditing purposes.

  <details>
    <summary>Example of Request Body for ERROR_REPORT callback type</summary>

    ``` json
      {
        "callbackType":"ERROR_REPORT",
        "data":{
            "verificationUuid":"64ab871b-14a3-47df-9b80-368e29fe8182",
            "result":"ERROR",
            "reason":"BLACKLISTED",
            "message":"Originator is included in the blacklist."
        }
      }
    ```
  </details>


  ** 5. CHAINALYSIS_KYT_RESULT Callback Handling**

  When a risk assessment result is received for a Chainalysis KYT request, your
  VASP can
    - Update the evaluation data for the associated originator or beneficiary.
    - Allow or deny the asset transfer based on the assessment result.

  <details>
    <summary>Example of Request Body for CHAINALYSIS_KYT_RESULT callback type</summary>

    ``` json
      {
        "callbackType":"CHAINALYSIS_KYT_RESULT",
        "data":{
            "verificationUuid":"69a310e6-810f-4a31-83d1-bcdafccf5304",
            "riskAssessment":{
              "chainalysisKYT":{
                  "requestId":"f7231c6f-f1e7-4ae7-b143-2c87cd38abe9",
                  "counterpartyVaspId":"15952089931162059995",
                  "apiType":"ATTEMPT",
                  "userId":"15952089931162059995",
                  "direction":"OUTGOING",
                  "network":"ETHEREUM",
                  "asset":"ETH",
                  "amount":"1",
                  "usdPrice":"1820.17",
                  "outputAddress":"bb3fd383d1c5540e52ef0a7bcb9433375793aeaf",
                  "timestamp":"2023-05-18T12:39:44.000Z",
                  "externalId":"79382ac9-c7be-3fab-ad56-8c61c654e2fc",
                  "status":"PROCESSED",
                  "alertCount":1,
                  "createdAt":"2023-05-18T12:39:46.000Z",
                  "assessedAt":"2023-05-18T12:39:45.263Z"
              },
              "chainalysisKYTAlerts":[
                  {
                    "counterpartyVaspId":"15952089931162059995",
                    "externalId":"79382ac9-c7be-3fab-ad56-8c61c654e2fc",
                    "direction":"OUTGOING",
                    "alertId":"118b8cc8-f579-11ed-b86d-a3210c6ca9b8",
                    "alertLevel":"MEDIUM",
                    "entityCategory":"high risk exchange",
                    "serviceName":"HIGH RISK EXCHANGE: SimpleSwap.io bb3fd383d1c5540e52ef0a7bcb9433375793aeaf",
                    "exposureType":"DIRECT",
                    "alertAmount":"1820.17",
                    "createdAt":"2023-05-18T12:39:52.461Z"
                  }
              ]
            }
        }
      }
    ```
  </details>


  ** 6. REFINITIV_WCO_RESULT Callback Handling**

  When a risk assessment result is received for a Refinitiv WCO request, your
  VASP can
    - Update the evaluation data for the associated originator or beneficiary.
    - Allow, resume, or deny the asset transfer based on the assessment result.

  <details>
    <summary>Example of Request Body for REFINITIV_WCO_RESULT callback type</summary>

    ``` json
      {
        "callbackType":"REFINITIV_WCO_RESULT",
        "data":{
            "verificationUuid":"69a310e6-810f-4a31-83d1-bcdafccf5304",
            "riskAssessment":{
              "refinitivWorldCheckOne":{
                  "counterpartyVaspId":"15952089931162058999",
                  "direction":"INCOMING",
                  "caseSystemId":"5jb7r2c9xjfk1hoc95gfayv6m",
                  "status":"PROCESSED",
                  "matchStrength":"EXACT",
                  "aggregatedSummaryResult":"{\"caseId\":\"69a310e6-810f-4a31-83d1-bcdafccf5304-INCOMING-1684413585757\", ... }}}",
                  "createdAt":"2023-05-18T12:39:48.000Z",
                  "assessedAt":"2023-05-18T12:39:57.834Z"
              }
            }
        }
      }
    ```
  </details>


  ## Constraints
    - This API must respond within 1 second.
    - Only the HTTP status code 200 OK can be returned. Other response status codes are not allowed.
    - To maintain data consistency and reliability, the Callback API must guarantee idempotency. 
      - This ensures that if the same Callback API request is received multiple times from the Enclave, the data state and response remain unchanged after the initial call. 
      - For example, you can implements your VASP to ignore the duplicate requests.

  ## Recommendations
    Since the API response must be returned as quickly as possible, time-consuming tasks within the Callback API should be handled asynchronously.

  ## Environment Variable Configuration
    Set the following environment variables as per the guide to integrate the implemented API with the Enclave. For a complete list of Enclave environment variables, [click here](ref:travelrule-enclave-setup).
    - `VEGA_VERIFICATION_CALLBACK_API_PATH`: Implement this API at the desired path({VASP_DEFINED_PATH_CALLBACK}) and set the path in the variable.
    - `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: Set this variable to the API key provided during your VerifyVASP onboarding process.
api:
  file: TR_VASP_API_KR_Spec.yaml
  operationId: travelrule-callback
hidden: false
---
## Callback API

Callback API는 Enclave에서 발생하는 다양한 비동기 이벤트를 처리하기 위한 공통 인터페이스입니다.\
요청의 `callbackType`에 따라 Originating VASP 또는 Beneficiary VASP 역할일 수 있으며, 모든 콜백 유형에 대해 정상 동작하도록 구현되어야 합니다.
이 API는 상대 VASP가 Report API를 호출하면 Enclave가 호출합니다.

### Functional Requirements

#### 1. 콜백 타입별 처리 로직 분기

콜백 요청의 `callbackType` 필드에 따라 적절한 비즈니스 로직으로 분기 처리해야 합니다. 다음은 지원해야 하는 콜백 타입입니다.

* `VERIFICATION_RESULT`: Originating VASP 역할일 때, 사용자 검증 결과가 비동기로 전달됨

* `TX_REPORT`: Beneficiary VASP 역할일 때, Originating VASP가 트랜잭션 실행 결과를 전달함

* `ERROR_REPORT`: Beneficiary VASP 역할일 때, Originating VASP가 오류 보고를 전달함\
  → 위 세 가지는 **필수 구현 대상입니다.**

* `CHAINALYSIS_KYT_RESULT`: Chainalysis KYT 결과가 도착했을 때

* `REFINITIV_WCO_RESULT`: Refinitiv WCO 결과가 도착했을 때\
  → 위 두 가지는 해당 리스크 평가 API를 사용하는 경우에만 **선택적 구현 대상입니다.**

#### 2. VERIFICATION\_RESULT (Originating VASP)

Originator 사용자에 대한 검증 결과가 도착하면, 해당 결과에 따라 다음 작업을 수행해야 합니다.

* 검증 성공 시: 수신자 검증을 이어서 진행하거나, 트랜잭션 실행 진행
* 검증 실패 시: 자산 출금 취소 처리 및 사용자에게 실패 사유 전송 (`data.reason` 활용)

#### 3. TX\_REPORT (Beneficiary VASP)

수신자가 실제로 입금을 받았는지 확인하기 위해 다음을 수행할 수 있습니다.

* 보고된 트랜잭션 해시와 수신 지갑 입금 정보 매칭
* 트랜잭션 수신 확인 및 기록 보관

#### 4. ERROR\_REPORT (Beneficiary VASP)

오류가 보고된 경우 다음과 같은 처리를 수행할 수 있습니다.

* 관련 자산 출금 요청 취소
* 트랜잭션 추적 중지 및 로그 기록

#### 5. CHAINALYSIS\_KYT\_RESULT

Chainalysis KYT 분석 결과 도착 시 다음과 같이 활용합니다.

* 결과를 기반으로 Originator 또는 Beneficiary의 평가 데이터 갱신
* 분석 결과에 따라 트랜잭션 허용 또는 차단 결정

#### 6. REFINITIV\_WCO\_RESULT

Refinitiv WCO 분석 결과 도착 시 다음과 같이 활용합니다.

* 결과를 기반으로 Originator 또는 Beneficiary의 평가 데이터 갱신
* 트랜잭션 허용, 재개 또는 차단 결정

***

## Reason Codes for `DENIED` and `ERROR` Results

아래 테이블은 `VERIFICATION_RESULT` 콜백에서 `DENIED` 또는 `ERROR` 결과가 발생했을 때 사용하는 사유 코드입니다.

| Reason                            | Result | Message 예시                                                     | 설명                                                    |
| --------------------------------- | ------ | -------------------------------------------------------------- | ----------------------------------------------------- |
| `UNKNOWN-SYMBOL`                  | DENIED | `"ETH"`                                                        | Originating VASP가 제공한 가상자산 종목이 수신 VASP에서 지원되지 않을 때    |
| `UNKNOWN-NETWORK`                 | DENIED | `"Ethereum"`                                                   | 네트워크 정보가 부족하거나 수신 VASP에서 해당 네트워크를 지원하지 않을 때           |
| `UNKNOWN-ADDRESS`                 | DENIED | `"0x... is not registered."`                                   | 수신 VASP가 해당 주소를 관리하지 않을 때                             |
| `LACK-OF-INFORMATION`             | DENIED | `"ACCOUNT_NUMBER"`                                             | 수신 VASP가 검증 수행에 필요한 Originator 정보가 부족한 경우             |
| `UNAVAILABLE-INFORMATION`         | DENIED | `"ACCOUNT_NUMBER"`                                             | Originating VASP가 요청한 정보를 수신 VASP가 보유하지 않거나 제공 불가능할 때 |
| `BLACKLISTED`                     | DENIED | `"0x.. is listed on the blacklist."`                           | Originator가 제재 목록에 포함되어 리스크 판단 결과 거절된 경우              |
| `UNVERIFIED-KYC`                  | DENIED | `"0x.. is unverified KYC"`                                     | 수신자가 KYC 미완료 상태일 때                                    |
| `MISMATCHED-NAME`                 | DENIED | `"Name is not matched."`                                       | 수신자의 이름이 Originator가 제공한 정보와 일치하지 않을 때                |
| `NOT-ALLOWED`                     | DENIED | `"This user is locked by internal policy."`                    | 내부 정책에 따라 수신자가 차단된 경우                                 |
| `UNDEFINED-ERROR`                 | DENIED | `"Undefined Error is occurred."`                               | 정의되지 않은 오류가 발생한 경우                                    |
| `BENEFICIARY-ACCOUNT-NOT-MATCHED` | ERROR  | `"Beneficiary account is not matched with requested account."` | 수신 VASP가 요청된 주소와 다른 주소를 반환한 경우                        |
| `REQUEST-TIMEOUT`                 | ERROR  | `"Request timeout."`                                           | 검증 요청이 허용된 시간 내에 완료되지 않은 경우                           |

***

## Constraints

* 이 API는 반드시 **1초 이내로 응답**해야 합니다.
* 응답은 반드시 HTTP status code 200 OK만 사용해야 하며, 다른 상태 코드는 허용되지 않습니다.
* **Idempotent 보장 필요**\
  동일한 요청이 여러 번 수신되더라도 결과가 달라지지 않도록 구현해야 합니다.

***

## Recommendations

* 응답 속도가 중요하므로, 시간 소모가 큰 작업은 비동기 처리하는 것을 권장합니다.

***

## Environment Variable Configuration

다음 환경 변수를 설정하여 구현한 API와 Enclave 간 연동을 완료하세요.

* `VEGA_VERIFICATION_CALLBACK_API_PATH`\
  해당 API의 엔드포인트 경로를 이 변수에 설정합니다. (`{VASP_DEFINED_PATH_CALLBACK}`)

* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`\
  VerifyVASP 가입 시 제공받은 API Key를 이 변수에 설정합니다.

Enclave 환경 변수 전체 목록은 [여기](ref:travelrule-enclave-setup)에서 확인할 수 있습니다.