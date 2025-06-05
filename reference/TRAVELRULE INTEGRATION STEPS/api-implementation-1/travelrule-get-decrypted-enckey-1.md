---
title: Database Management API
api:
  file: TR_VASP_API_KR_Spec.yaml
  operationId: travelrule-get-Decrypted-EncKey
hidden: false
---
## Get Database Encryption Key API

이 API는 Enclave 데이터베이스에 저장되는 민감 정보를 암호화하기 위한 대칭키를 안전하게 관리할 수 있는 선택적 방식입니다.\
Enclave는 민감한 데이터를 평문으로 저장하지 않고, VASP가 제공한 대칭키로 암호화하여 저장합니다. 이 키는 기본적으로 `VEGA_ENCRYPTION_KEY_BASE64` 환경 변수에 설정됩니다.

그러나 암호화 키를 환경 변수에 직접 저장하면 보안상 위험이 존재합니다. 이를 해결하기 위해 **암호화된 키**, **Key ID**, **리소스 이름** 등의 참조값만 환경 변수에 설정하고, 해당 값을 기반으로 실제 키를 API를 통해 받아오는 방식을 사용할 수 있습니다.

이 API를 구현하면 Enclave는 참조값만 보유한 상태에서, 필요 시 해당 API를 호출하여 실제 키를 안전하게 로딩할 수 있습니다.

***

## How Enclave Works

* `VEGA_ENCRYPTION_KEY_BASE64`만 설정된 경우:\
  Enclave는 이 값을 그대로 사용하여 데이터를 암호화합니다.

* `VEGA_ENCRYPTION_KEY_BASE64`와 `VEGA_DECRYPT_API_ENDPOINT`가 모두 설정된 경우:\
  Enclave는 환경 변수에 설정된 참조값(inputKey)을 사용하여 이 API를 호출하고, 응답으로 받은 `outputKey` 값을 암호화에 사용합니다.

***

## Functional Requirements

### 1. 비 API 요구사항: 암호화 키 생성 및 관리

* VASP는 데이터베이스 암호화를 위한 대칭키를 생성하고 안전하게 관리할 수 있는 체계를 갖추어야 합니다.
* 자체 암호화 모듈을 사용하거나 KMS(Key Management System)와 같은 외부 보안 시스템을 사용할 수 있습니다.

### 2. API 요구사항

* 요청의 `inputKey` 필드로 전달된 참조값을 기반으로 실제 대칭키를 조회해야 합니다.
  * 참조값이 암호화된 키일 경우 복호화 처리
  * 참조값이 KMS 키 ID인 경우, KMS에서 키를 조회
* 조회된 실제 키는 응답의 `outputKey` 필드에 담아 반환해야 합니다.
* Enclave는 이 키를 사용해 민감 데이터를 암호화합니다.

***

## Recommendations

* 대칭키를 평문으로 `VEGA_ENCRYPTION_KEY_BASE64`에 직접 저장하지 마세요.\
  보안 위험이 크므로 참조값만 저장하는 방식이 권장됩니다.

* AWS KMS와 같은 외부 키 관리 시스템과 연동하는 방식이 이상적입니다.
  * 예시:
    * `VEGA_ENCRYPTION_KEY_BASE64`: AWS KMS 키 ARN
    * `VEGA_DECRYPT_API_ENDPOINT`: 키 조회 및 복호화를 수행하는 자체 서버 API 주소

***

## Environment Variable Configuration

다음 환경 변수를 설정하여 구현한 API를 Enclave와 연동하세요.\
전체 환경 변수 목록은 [여기](ref:travelrule-enclave-setup)에서 확인할 수 있습니다.

* `VEGA_DECRYPT_API_ENDPOINT`\
  이 API의 엔드포인트 경로를 설정하세요. (`{VASP_DEFINED_PATH_DECRYPT_DB_ENCKEY}`)

* `VEGA_ENCRYPTION_KEY_BASE64`\
  참조값(암호화된 키, 키 ID 등)을 이 변수에 설정하세요.