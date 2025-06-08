---
title: Enclave Setup
excerpt: >-
  Enclave 데이터베이스 설정이 완료되었다면, Enclave Docker 이미지를 다운로드 받아 서버를 구동하십시오. 본 문서는
  Enclave 구동과 환경변수 설정과 관련된 가이드를 제공합니다.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Enclave Docker 이미지 준비

VerifyVASP에서 개발한 Enclave 서버는 Private Docker Hub 레지스트리를 통해 Docker 이미지 형태로 배포됩니다. 레지스트리 접근 권한을 얻기 위해 본인의 Docker Hub ID를 [corporate@verifyvasp.com](mailto:corporate@verifyvasp.com) 주소로 제출하십시오.

## Enclave 서버 실행 및 환경 변수 설정

레지스트리 접근 권한이 부여되었다면, 이제 VASP 내부 인프라 환경에서 Enclave 서버를 다운로드 및 실행해야합니다. Enclave 서버와 관련 컴포넌트 간의 원활한 통신을 위해서는 서버를 실행하기에 앞서 필수 환경 변수들을 먼저 설정해야 합니다.

Enclave의 환경 변수는 아래 다섯가지 주요 그룹으로 분류됩니다.

<HTMLBlock>{`
.enclave-env-table {
  width: 100%;
  background-color: white;
  border-collapse: collapse;
  table-layout: fixed;
}

.enclave-env-table th,
.enclave-env-table td {
  background-color: white;
  border: 1px solid #ddd;
  padding: 12px;
  vertical-align: top;
  word-break: break-word;
}

.enclave-env-table th {
  text-align: left;
  font-weight: bold;
  background-color: white;
}

<table class="enclave-env-table">
  <thead>
    <tr>
      <th>환경변수 그룹</th>
      <th>설명</th>
      <th>예시</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>서버 및 데이터베이스 설정 변수</strong></td>
      <td>서버 실행 및 데이터베이스 접속을 위해 필요한 설정</td>
      <td>서버 엔드포인트/포트, DB 사용자명/비밀번호, DB 접속 설정 등</td>
    </tr>
    <tr>
      <td><strong>인증 관련 변수</strong></td>
      <td>VerifyVASP 중앙 서버 및 3rd Party 서비스 연동을 위한 인증 정보</td>
      <td>access key/secret key, Chainalysis API key, Refinitiv API key 등</td>
    </tr>
    <tr>
      <td><strong>VASP API 엔드포인트</strong></td>
      <td>Enclave가 VASP API를 호출할 때 사용되는 API 엔드포인트</td>
      <td>사용자 계정 검증 API, 사용자 정보 검증 API 등</td>
    </tr>
    <tr>
      <td><strong>보안 및 설정 관련 변수</strong></td>
      <td>Enclave 서버의 보안 수준 및 운영 설정을 위한 변수</td>
      <td>공개키 캐시 옵션 등</td>
    </tr>
    <tr>
      <td><strong>Enclave 모드 설정</strong></td>
      <td>Enclave의 구동 모드, 지원 프로토콜에 따라 복수 설정</td>
      <td>TR(TravelRule), VN1_CALL/VN1_RESPONSE(VerifyName 1.0), VN2 등</td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

<br />

#### 1. 서버 및 데이터베이스 설정 변수

* 서버 실행 및 데이터베이스 접속을 위해 필요한 설정
* (ex) 서버 엔드포인트/포트, DB 사용자명/비밀번호, DB 접속 설정 등

#### 2. 인증 관련 변수

* VerifyVASP 중앙 서버 및 3rd Party 서비스 연동을 위한 인증 정보
* 예: Alliance access key/secret key, Chainalysis API key, Refinitiv API key 등

#### 3. VASP API 엔드포인트

* Enclave가 VASP API를 호출할 때 사용되는 API 엔드포인트
* 예: 사용자 계정 검증 API, 사용자 정보 검증 API 등

#### 4. 보안 및 설정 관련 변수

* Enclave 서버의 보안 수준 및 운영 설정을 위한 변수
* 예: 공개키 캐시 옵션 등

#### 5. Enclave 모드 설정

* Enclave 서버를 실행하는 목적에 따라 설정할 수 있는 Enclave의 구동 모드
* TR
* VN1\_CALL (곧 지원 종료 예정)
* VN1\_RESPONSE (곧 지원 종료 예정)
* VN2

위 값들은 VEGA\_ENCLAVE\_MODE 환경 변수에 콤마(,)로 구분하여 입력할 수 있으며, 최소 한 개 이상을 지정해야 합니다.

예시: VEGA\_ENCLAVE\_MODE=TR,VN2,VN1\_CALL,VN1\_RESPONSE

TR만 설정된 경우: Travel Rule API 요청/응답 처리를 위한 Enclave 서버 운영을 의미합니다.

VN1\_CALL만 설정된 경우: VerifyName V1 API 요청 처리를 위한 Enclave 서버 운영을 의미합니다.

VN1\_RESPONSE만 설정된 경우: VerifyName V1 API 응답 처리를 위한 Enclave 서버 운영을 의미합니다.

VN2만 설정된 경우: VerifyName V2 API 요청 및 응답 처리를 위한 Enclave 서버 운영을 의미합니다.

둘 이상 설정된 경우: 복수 역할을 동시에 수행합니다.

환경 변수에 대한 목적, 기본값, 설정 방법을 요약한 표는 다음과 같습니다. 환경 변수의 이름은 절대 변경되어서는 안 됩니다.

<br />

<Table align={["left","left","left"]}>
  <thead>
    <tr>
      <th>
        Variable Name
      </th>

      <th>
        Default Value
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        `VEGA_SERVER_PORT`
      </td>

      <td>
        21117
      </td>

      <td>
        Enclave Server Port Number.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_ENCLAVE_MODE`
      </td>

      <td>

      </td>

      <td>
        Configuration based on the purpose of using the Enclave server.
        You can select either a single mode or multiple modes by separating them with commas from the available options.

        <br />

        Available modes:
        `TR`, `VN2`, `VN1_CALL`, `VN1_RESPONSE`
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_ENCLAVE_PUBLIC_ENDPOINT`
      </td>

      <td>
        –
      </td>

      <td>
        Public Endpoint URL for VASP's Enclave Server, accessible by VerifyVASP Central Server. Must use the HTTPS protocol. Example: `https://api.vasp.com/enclave`
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_ALLIANCE_ACCESS_KEY`
      </td>

      <td>
        –
      </td>

      <td>
        The API Access Key issued via VerifyVASP Console.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_ALLIANCE_SECRET_KEY`
      </td>

      <td>
        –
      </td>

      <td>
        The API Secret Key issued via VerifyVASP Console.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_DATABASE_CLIENT`
      </td>

      <td>
        `mysql2`
      </td>

      <td>
        Type of the database. Acceptable values: `pg`, `mysql`, `mysql2`, `oracledb`, `mssql`
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_DATABASE_USERNAME`
      </td>

      <td>
        –
      </td>

      <td>
        Database user name.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_DATABASE_PASSWORD`
      </td>

      <td>
        –
      </td>

      <td>
        Database password.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_DATABASE_HOST`
      </td>

      <td>
        –
      </td>

      <td>
        Address to access the database.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_DATABASE_PORT`
      </td>

      <td>
        `3306`
      </td>

      <td>
        Port number to access the database.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_DATABASE_DB`
      </td>

      <td>
        `verifyvasp`
      </td>

      <td>
        Database name that the Enclave server uses.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_DATABASE_SEARCH_PATH`
      </td>

      <td>
        `enclave`
      </td>

      <td>
        Custom schema name for PostgreSQL.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_DATABASE_POOL_MIN`
      </td>

      <td>
        `0`
      </td>

      <td>
        Minimum value of database connection pool.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_DATABASE_POOL_MAX`
      </td>

      <td>
        `5`
      </td>

      <td>
        Maximum value of database connection pool.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_DATABASE_SSL`
      </td>

      <td>
        `true / false`
      </td>

      <td>
        Enable or disable SSL connection to the database.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_DATABASE_SSL_CA`
      </td>

      <td>
        –
      </td>

      <td>
        Path to CA Certificate File for Server Authentication.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_DATABASE_SSL_KEY`
      </td>

      <td>
        –
      </td>

      <td>
        Path to Client Certificate Private Key File for SSL Connection.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_DATABASE_SSL_CERT`
      </td>

      <td>
        –
      </td>

      <td>
        Path to Client Public Certificate File for SSL Connection.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_DATABASE_SSL_CAPATH`
      </td>

      <td>
        –
      </td>

      <td>
        Directory path that stores CA certificates.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_DATABASE_SSL_CIPHER`
      </td>

      <td>
        –
      </td>

      <td>
        Encryption algorithms for client-server communication.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_DATABASE_SSL_REJECT_UNAUTHORIZED`
      </td>

      <td>
        `true / false`
      </td>

      <td>
        Allow SSL connection even if the server certificate is untrusted.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_VERIFICATION_CALLBACK_API_PATH`
      </td>

      <td>
        –
      </td>

      <td>
        Callback API (VASP API) endpoint URL.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`
      </td>

      <td>
        –
      </td>

      <td>
        Authorization token value for Enclave-to-VASP Backend API calls. Sent in the header.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_VERIFICATION_AUTHORIZATION_KEY`
      </td>

      <td>
        –
      </td>

      <td>
        Header key used to deliver the authorization token. Defaults to `Authorization` if not set.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_API_ENDPOINT`
      </td>

      <td>
        –
      </td>

      <td>
        VerifyVASP Central API Server Address.

        * PRD (KR): `https://api-kr.vega-protocol.com`
        * PRD (Global): `https://api.vega-protocol.com`
        * STG (KR): `https://api-kr.vega-protocol.xyz`
        * STG (Global): `https://api.vega-protocol.xyz`

        <br />
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_LOG_LEVEL`
      </td>

      <td>
        `info`
      </td>

      <td>
        Log level. Acceptable values: `none`, `error`, `warn`, `info`, `debug`
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_PUBLIC_KEY_TTL`
      </td>

      <td>
        `1800000`
      </td>

      <td>
        Cache duration for the counterparty VASP's public key (in ms). Default: 1800000 (30 min), Minimum: 600000 (10 min)
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_DECRYPT_API_ENDPOINT`
      </td>

      <td>
        –
      </td>

      <td>
        Endpoint of external service for decrypting `VEGA_ENCRYPTION_KEY_BASE64`.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_ENCRYPTION_KEY_BASE64`
      </td>

      <td>
        –
      </td>

      <td>
        Encryption key for personal data. Either used directly or sent to `VEGA_DECRYPT_API_ENDPOINT` to retrieve the actual key.
      </td>
    </tr>
  </tbody>
</Table>

<br />

네트워크 설정

Enclave 서버가 정상적으로 실행된 이후, VerifyVASP 중앙 서버와의 안전한 통신을 위해 네트워크 화이트리스트를 구성해야 합니다.

Enclave 서버 IP를 VerifyVASP 중앙 서버에 등록

VASP의 Enclave 서버 IP를 [corporate@verifyvasp.com](mailto:corporate@verifyvasp.com) 으로 전달하면 VerifyVASP에서 허용 목록에 등록합니다.

등록 가능한 값은 IP 주소에 한정되며, 도메인 주소는 허용되지 않습니다.

VerifyVASP 중앙 서버의 IP를 VASP 측에 등록

VerifyVASP 중앙 서버가 VASP의 인프라에 접근할 수 있도록, VASP 측에서도 VerifyVASP의 IP 주소를 허용 목록에 등록해야 합니다.

해당 IP 정보는 [corporate@verifyvasp.com](mailto:corporate@verifyvasp.com) 또는 전용 Slack 채널을 통해 문의해 주세요.

Enclave 통합 API 구성

이제 출금 프로세스 내 Travel Rule을 연동할 시점입니다. 출금 절차의 각 단계에서 어떤 Enclave API를 호출해야 하는지 확인하려면 Best Practice 시퀀스 다이어그램을 참조하세요.

각 Enclave API 명세는 아래 링크에서 확인할 수 있으며, 통합 구현이 완료된 이후 양측 통신이 원활한지 테스트를 통해 검증하세요.