---
title: Verify User API
api:
  file: TR_VASP_API_KR_Spec.yaml
  operationId: travelrule-User-Verification
hidden: false
---
VASP는 TravelRule 프로토콜 내에서 송신 VASP와 수신 VASP의 역할을 모두 수행해야 합니다. 이 API는 수신 VASP 역할을 위한 구현 요구사항입니다. 수신자의 개인 정보(이름, 주소 등)가 VASP의 KYC를 통해 확보한 정보와 일치하는지 검증하고, KYC/AML 및 제재 목록 대조 등 VASP 정책에 따른 컴플라이언스 결과를 수행한 뒤 검증 결과를 반환합니다.

***

## 구현 가이드

### 기능 요구사항

**1. 개인 정보 검증**

IVMS101 포맷으로 전달된 수신자 개인정보를 귀사 VASP의 보유 정보와 대조하여 일치 여부를 검증해야합니다.

**2. 컴플라이언스 확인**

다음 사항들을 검증하여 컴플라이언스 요건을 충족해야 합니다:

* KYC 완료 여부
* AML 정책 충족 여부
* 송신자에 대한 STR(의심거래보고) 및 제재 목록 조회
* 필요 시 추가적인 필터링 수행 가능

**3. 송신 VASP가 요청한 정보 응답**

* 송신 VASP의 `requiredBeneficiaryInfo`에 따라 필요한 정보를 `ivms101` 객체에 채워 응답해야 합니다.
* 정보가 없거나 제공 불가능할 경우, `verificationResult`는 `DENIED`, `reason`은 `UNAVAILABLE-INFORMATION`으로 설정해야 합니다.
* 요청되지 않은 항목은 빈 값으로 유지합니다.
* 지갑 주소는 원본 그대로 반환해야 하며, 잘못된 경우에는 `DENIED` 처리합니다.

**4. 검증 결과 응답**

* 검증 결과는 `VERIFIED` 또는 `DENIED`로 반환합니다.
  * 모든 정보가 일치하고 이슈가 없을 경우: `VERIFIED`
  * 정보 불일치, 정보 부족, 고위험 사용자 등인 경우: `DENIED`
* 검증 성공 시, 수신자 정보를 IVMS101 포맷으로 함께 반환해야 합니다.

**5. 실패 사유 코드**

* `verificationResult: DENIED`일 경우, 아래 사유 코드를 `reason` 필드에 지정해야 합니다:
  * `UNKNOWN-SYMBOL`: 지원하지 않는 가상자산
  * `UNKNOWN-NETWORK`: 지원하지 않는 네트워크
  * `UNKNOWN-ADDRESS`: 미등록 지갑 주소
  * `LACK-OF-INFORMATION`: 송신자 정보 부족
  * `UNAVAILABLE-INFORMATION`: 제공 불가한 수신자 정보
  * `BLACKLISTED`: 제재 목록 포함 주소
  * `UNVERIFIED-KYC`: KYC 미완료
  * `MISMATCHED-NAME`: 수신자 이름 불일치
  * `NOT-ALLOWED`: 내부 정책으로 인해 거부됨
  * `UNDEFINED-ERROR`: 정의되지 않은 오류

### 제약 조건

* 응답 시간은 5초 이내여야 합니다.

### 참고 사항

* 규제 기준 이하의 소액 전송은 Travel Rule 대상이 아니며, 수신자 이름 검증은 선택 사항입니다.
* 다만, 모든 트랜잭션에 TravelRule 적용을 권장하며, 규제 기준은 관할 구역별로 상이할 수 있습니다.
* 송신 VASP 요청에 `isExceedingThreshold: false`로 표시된 경우, 추가 검증 없이 전송할 수 있습니다.

### Enclave 연동 설정

* 다음 환경 변수를 설정해야 Enclave와 연동됩니다:
  * `VEGA_VERIFICATION_API_PATH`: 해당 API 구현 경로
  * `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: 온보딩 과정에서 발급받은 API 키