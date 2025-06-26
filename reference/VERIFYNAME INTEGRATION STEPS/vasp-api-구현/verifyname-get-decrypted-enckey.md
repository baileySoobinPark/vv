---
title: Database Management API
api:
  file: VN_VASP_API_Spec.yaml
  operationId: verifyName-get-Decrypted-EncKey
hidden: false
---
본 API는 Enclave 데이터베이스 암호화를 위한 대칭키를 Enclave로 동적으로 전달하기 위한 선택 구현 요구사항입니다.

***

<DatabaseAPI />

## Enclave의 민감 데이터 암호화를 위한 대칭 키 주입 방식

VerifyVASP Enclave는 민감 정보를 데이터베이스에 평문으로 저장하지 않으며, 대칭 키 기반 암호화를 통해 데이터를 보호합니다. 저장 시에는 암호화된 형태로 기록되며, 조회 시 복호화하여 사용합니다. 이때 사용될 대칭키는 VASP가 생성하여 주입해야 하는데, VerifyVASP Enclave는 (1)환경 변수에 직접 대칭 키를 설정하는 방식과 (2)본 API를 구현하여 Enclave가 대칭 키를 조회하여 사용하도록 하는 두가지 방식을 모두 지원합니다.

환경 변수를 통한 대칭키 주입 방식은 API의 구현 없이도 쉽게 키를 설정 가능하다는 장점이 있습니다. 그러나 이 방식은 키가 파일 시스템 수준에 저장되는 구조로, 보안 위협에 취약할 수 있습니다.

본 API를 구현하여 제공하는 경우 Enclave가 대칭 키를 런타임에 조회하여 메모리 상에서 로드 및 사용할 수 있어 보다 안전한 키 사용이 가능합니다. 또한 VASP 백엔드에서 외부 키 관리 시스템(KMS)과의 연계를 통해 키 보호 수준을 더욱 높일 수 있습니다. 이러한 이유로 가능한 경우 API를 통한 키 주입 방식을 사용하는 것을 권장합니다.

* 환경 변수를 통해 직접적으로 키를 주입하고자 하는 경우,`VEGA_ENCRYPTION_KEY_BASE64`에 대항 키 값을 설정합니다. Enclave는 이 값을 그대로 데이터 암복호화에 사용합니다.

* API를 통해 키를 주입하고자 하는 경우, `VEGA_ENCRYPTION_KEY_BASE64` 에는 해당 키에 대한 참조값(요청의 `inputKey`필드에 사용)을, `VEGA_DECRYPT_API_ENDPOINT`에는 본 API의 구현 경로를 설정합니다. Enclave는 설정된 참조값을 사용하여 이 API를 호출하고, 응답으로 받은 `outputKey` 값을 암호화에 사용합니다.

## 구현 가이드

### 기능 요구사항

#### 1. 암호화 키 생성 및 관리

* VASP는 데이터베이스 암호화를 위한 대칭키를 생성하고 안전하게 관리할 수 있는 체계를 갖추어야 합니다.
* 자체 암호화 모듈을 사용하거나 KMS(Key Management System)와 같은 외부 보안 시스템을 사용할 수 있습니다.

#### 2. 키 조회 및 반환

* 요청의 `inputKey` 필드로 전달된 참조값을 기반으로 실제 대칭키를 조회해야 합니다.
  * 참조값이 암호화된 키일 경우 복호화 처리
  * 참조값이 KMS 키 ID인 경우, KMS에서 키를 조회
* 조회된 실제 키는 응답의 `outputKey` 필드에 담아 반환해야 합니다.

### 권장 구현사항

* 대칭키를 평문으로 `VEGA_ENCRYPTION_KEY_BASE64`에 직접 저장하지 마십시오.\
  보안 위험이 크므로 참조값만 저장하는 방식을 권장합니다.

* AWS KMS와 같은 외부 키 관리 시스템과 연동하는 방식을 권장합니다.

  \[연동 예시]

  * `VEGA_ENCRYPTION_KEY_BASE64`: AWS KMS 키 ARN
  * `VEGA_DECRYPT_API_ENDPOINT`: 키 조회 및 복호화를 수행하는 자체 서버 API 주소

### Enclave 연동 설정

Enclave와의 정상 연동을 위해 아래 환경 변수를 설정해야 합니다.

* `VEGA_DECRYPT_API_ENDPOINT`: 해당 API 경로

* `VEGA_ENCRYPTION_KEY_BASE64`: 대칭키의 참조값

***

## API 명세