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
api:
  file: 2025_05_02_TR_VASP_API_Spec.yaml
  operationId: callback
hidden: false
---