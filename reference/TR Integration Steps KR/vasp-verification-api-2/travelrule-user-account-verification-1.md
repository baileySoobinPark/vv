---
title: Verify User Account API
excerpt: >
  VASP는 TravelRule 프로토콜 내에서 송신 VASP와 수신 VASP의 역할을 모두 수행해야 합니다. 이 API는 **수신
  VASP** 역할을 위한 구현 요구사항입니다. 송신자가 지정한 수신 주소가 VASP의 소유 주소인지 여부를 검증하고 결과를 반환합니다.


  ### 기능 요구사항


  **1. VASP의 주소 소유 여부 검증**  

  요청에 포함된 수신 주소(beneficiary.accountNumber)가 VASP로부터 발급된 주시인지 여부를 데이터베이스와 대조하여
  확인하고, 결과를 응답합니다.


  **2. 검증 결과 응답**  

  - VASP 소유 주소인 경우(검증 성공): `accountVerificationResult` 값을 `VERIFIED`로 반환  

  - 주소가 소유 주소가 아닌 경우(검증 실패): `accountVerificationResult` 값을 `DENIED`로 반환


  **3. 실패 사유 응답**  

  `DENIED`일 경우, `reason` 필드에 아래의 사유 코드 중 하나를 반드시 포함해야 합니다:


  | 사유 코드             | 설명 |

  |----------------------|------|

  | `UNKNOWN-SYMBOL`     | 지원하지 않는 가상자산 종목 (예: 거래소에서 미지원인 종목) |

  | `UNKNOWN-NETWORK`    | 지원하지 않는 네트워크 (예: USDT-Ethereum 요청되었으나 거래소에서
  USDT-Tron만 지원하는 경우) |

  | `UNKNOWN-ADDRESS`    | 해당 주소를 관리하지 않음 |

  | `MISMATCHED-NAME`    | 수신자 이름 불일치 |

  | `UNVERIFIED-KYC`     | KYC 미완료 사용자 |

  | `UNDEFINED-ERROR`    | 정의되지 않은 기타 오류 |


  ### 제약 사항
    * 이 API는 1초 이내에 응답해야 합니다.

  ### 구현 권장사항
    * 이 API는 주소 소유 여부만 검증하며, KYC/제재 조회 등 리스크 평가 로직은 포함하지 않는 것을 권장합니다.

  ### 환경 변수 설정
    Enclave와의 정상 연동을 위해 아래와 같이 Enclave 환경 변수를 설정해야합니다.
    * `VEGA_VERIFICATION_ACCOUNT_API_PATH`: 해당 API의 경로({VASP_CUSTOM_PATH_VERIFY_USER_ACCOUNT})
    * `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: VerifyVASP 온보딩 시 발급받은 API Key
api:
  file: TR_VASP_API_KR_Spec.yaml
  operationId: travelrule-User-Account-Verification
hidden: false
---