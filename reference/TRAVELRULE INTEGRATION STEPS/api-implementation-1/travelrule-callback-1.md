---
title: Callback API
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