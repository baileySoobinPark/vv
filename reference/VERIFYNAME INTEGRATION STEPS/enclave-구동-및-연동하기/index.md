---
title: Enclave 구동 및 연동하기
excerpt: >-
  Enclave 데이터베이스 설정이 완료되었다면, Enclave Docker 이미지를 다운로드 받아 서버를 구동하십시오. 본 문서는
  Enclave 구동과 환경변수 설정과 관련된 가이드를 제공합니다.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Step 1. Enclave Docker 이미지 준비

VerifyVASP Enclave 서버는 AWS ECR 레지스트리를 통해 Docker 이미지 형태로 배포됩니다. 접근 권한을 얻기 위해 AWS CLI Access Key를 수령하실 이메일 주소를 [support@verifyvasp.com](mailto:support@verifyvasp.com) 로 전달해주세요.

<Enclave />

<br />

## Step 2. Enclave 서버 실행 및 환경 변수 설정

접근 권한이 부여되었다면, 이제 VASP 내부 인프라 환경에서 Enclave 서버를 다운로드하고 실행해야 합니다. 다만 Enclave 서버와 관련 컴포넌트 간의 원활한 통신을 위해, 서버를 실행하기 전 필수 환경 변수들을 먼저 설정해야 합니다.

Enclave의 환경 변수는 아래 다섯 가지 주요 그룹으로 분류됩니다.

<HTMLBlock>{`
<style>
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
  background-color: #f0f0f0;
}
</style>
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
      <td>서버 및 데이터베이스 설정 변수</td>
      <td>서버 실행 및 데이터베이스 접속을 위해 필요한 설정</td>
      <td>서버 엔드포인트/포트, DB 사용자명/비밀번호, DB 접속 설정 등</td>
    </tr>
    <tr>
      <td>인증 관련 변수</td>
      <td>VerifyVASP 중앙 서버 및 3rd Party 서비스 연동을 위한 인증 정보</td>
      <td>access key / secret key 등</td>
    </tr>
    <tr>
      <td>VASP API 엔드포인트</td>
      <td>Enclave가 VASP API를 호출할 때 사용되는 API 엔드포인트</td>
      <td>소유주 검증(VerifyName) API Endpoint 등</td>
    </tr>
    <tr>
      <td>보안 및 설정 관련 변수</td>
      <td>Enclave 서버의 보안 수준 및 운영 설정을 위한 변수</td>
      <td>공개키 캐시 옵션 등</td>
    </tr>
    <tr>
      <td>Enclave 모드 설정</td>
      <td>Enclave의 구동 모드, 지원 프로토콜에 따라 복수 설정</td>
      <td>TR(TravelRule), VN1_CALL/VN1_RESPONSE(VerifyName 1.0), VN2 등</td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

환경 변수에 대한 목적, 기본값, 설정 방법을 요약한 표는 다음과 같습니다. 환경 변수의 이름은 **반드시 변경 없이 그대로** 사용하십시오.

<HTMLBlock>{`
<style>
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
  background-color: #f0f0f0;
}
</style>
<Table align={["left","left","left"]} class="enclave-env-table">
  <thead>
    <tr>
      <th style={{ textAlign: "left" }}>
        Variable Name
      </th>

      <th style={{ textAlign: "left" }}>
        Default Value
      </th>

      <th style={{ textAlign: "left" }}>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_SERVER_PORT\`
      </td>

      <td style={{ textAlign: "left" }}>
        21117
      </td>

      <td style={{ textAlign: "left" }}>
        Enclave 서버 포트 번호.
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_ENCLAVE_MODE\`
      </td>

      <td style={{ textAlign: "left" }}>

      </td>

      <td style={{ textAlign: "left" }}>
        Enclave의 구동모드.
        Enclave를 통해 지원하고자 하는 VerifyVASP의 프로토콜들을 명시합니다.

        단일 또는 복수 설정이 가능하며, 복수 설정시 모드 값을 콤마(,)로 구분하여 모두 입력합니다. 복수 설정시 설정된 모드들을 동시 지원합니다.

        **사용 가능한 모드 값** : \`TR\`, \`VN2\`, \`VN1_CALL\`, \`VN1_RESPONSE\`

        (ex) \`VEGA_ENCLAVE_MODE=TR,VN2\`
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_ENCLAVE_PUBLIC_ENDPOINT\`
      </td>

      <td style={{ textAlign: "left" }}>
        –
      </td>

      <td style={{ textAlign: "left" }}>
        Enclave 서버의 Public Endpoint URL.\
        VerifyVASP 중앙서버로부터 접근 가능한HTTPS 주소를 입력해야합니다.

        \`https://api.vasp.com/enclave\`
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_ALLIANCE_ACCESS_KEY\`
      </td>

      <td style={{ textAlign: "left" }}>
        –
      </td>

      <td style={{ textAlign: "left" }}>
        VerifyVASP API Access Key.\
        온보딩 시 발급받은 값을 입력합니다.

        **관련 링크**: [Onboarding](ref:onboarding-copy-1)
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_ALLIANCE_SECRET_KEY\`
      </td>

      <td style={{ textAlign: "left" }}>
        –
      </td>

      <td style={{ textAlign: "left" }}>
        VerifyVASP API Secret Key.\
        온보딩 시 발급받은 값을 입력합니다.

        **관련 링크**: [Onboarding](ref:onboarding-copy-1)
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_DATABASE_CLIENT\`
      </td>

      <td style={{ textAlign: "left" }}>
        \`mysql2\`
      </td>

      <td style={{ textAlign: "left" }}>
        Enclave 데이터베이스의 DBMS 유형.\
        **사용 가능한 값**: \`pg\`, \`mysql\`, \`mysql2\`, \`oracledb\`, \`mssql\`

        **관련 링크**: [Enclave Database Setup](ref:database-setup-copy)
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_DATABASE_USERNAME\`
      </td>

      <td style={{ textAlign: "left" }}>
        –
      </td>

      <td style={{ textAlign: "left" }}>
        Database user name.
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_DATABASE_PASSWORD\`
      </td>

      <td style={{ textAlign: "left" }}>
        –
      </td>

      <td style={{ textAlign: "left" }}>
        Database password.
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_DATABASE_HOST\`
      </td>

      <td style={{ textAlign: "left" }}>
        –
      </td>

      <td style={{ textAlign: "left" }}>
        데이터베이스 연동을 위한 Host URL.
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_DATABASE_PORT\`
      </td>

      <td style={{ textAlign: "left" }}>
        \`3306\`
      </td>

      <td style={{ textAlign: "left" }}>
        데이터베이스 포트 번호.
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_DATABASE_DB\`
      </td>

      <td style={{ textAlign: "left" }}>
        \`verifyvasp\`
      </td>

      <td style={{ textAlign: "left" }}>
        데이터베이스 DB 이름.
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_DATABASE_SEARCH_PATH\`
      </td>

      <td style={{ textAlign: "left" }}>
        \`enclave\`
      </td>

      <td style={{ textAlign: "left" }}>
        Custom schema 이름(PostgreSQL 케이스에 한함).
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_DATABASE_POOL_MIN\`
      </td>

      <td style={{ textAlign: "left" }}>
        \`0\`
      </td>

      <td style={{ textAlign: "left" }}>
        최소 데이터베이스 Connection Pool 크기.
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_DATABASE_POOL_MAX\`
      </td>

      <td style={{ textAlign: "left" }}>
        \`5\`
      </td>

      <td style={{ textAlign: "left" }}>
        최대 데이터베이스 Connection Pool 크기.
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_DATABASE_SSL\`
      </td>

      <td style={{ textAlign: "left" }}>
        \`false\`
      </td>

      <td style={{ textAlign: "left" }}>
        데이터베이스 SSL 연결 사용 여부\
        (사용시 \`true\`로 설정)
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_DATABASE_SSL_CA\`
      </td>

      <td style={{ textAlign: "left" }}>
        –
      </td>

      <td style={{ textAlign: "left" }}>
        데이터베이스 SSL 연결을 위한 CA 인증서 파일 위치
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_DATABASE_SSL_KEY\`
      </td>

      <td style={{ textAlign: "left" }}>
        –
      </td>

      <td style={{ textAlign: "left" }}>
        데이터베이스 SSL 연결을 위한 클라이언트 SSL 인증서 Private Key 경로.
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_DATABASE_SSL_CERT\`
      </td>

      <td style={{ textAlign: "left" }}>
        –
      </td>

      <td style={{ textAlign: "left" }}>
        데이터베이스 SSL 연결을 위한 클라이언트 SSL 인증서 경로.
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_DATABASE_SSL_CAPATH\`
      </td>

      <td style={{ textAlign: "left" }}>
        –
      </td>

      <td style={{ textAlign: "left" }}>
        데이터베이스 SSL 연결을 위한 CA 인증서 디렉토리 경로
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_DATABASE_SSL_CIPHER\`
      </td>

      <td style={{ textAlign: "left" }}>
        –
      </td>

      <td style={{ textAlign: "left" }}>
        데이터베이스 SSL 연결을 위한 암호화 방식
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_DATABASE_SSL_REJECT_UNAUTHORIZED\`
      </td>

      <td style={{ textAlign: "left" }}>
        \`true\`
      </td>

      <td style={{ textAlign: "left" }}>
        데이터베이스 SSL 연결시 인증서 인증 오류시 연결 거절 여부 (거절시 true로 설정)
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_VERIFICATION_VERIFY_NAME_V2_API_PATH\`
      </td>

      <td style={{ textAlign: "left" }}>

      </td>

      <td style={{ textAlign: "left" }}>
        \`VerifyName\`\
        VASP API 호출을 위한 [Verify Name API](ref:verifyname-request-verification) Endpoint.
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_VERIFICATION_VERIFY_NAME_  
                TRANSACTION_API_PATH\`
      </td>

      <td style={{ textAlign: "left" }}>

      </td>

      <td style={{ textAlign: "left" }}>
        \`VerifyName\`\
        VASP API 호출을 위한 VerifyName [Check Transaction Status API](ref:verifyname-transaction) Endpoint.
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_VERIFICATION_CALLBACK_API_PATH\`
      </td>

      <td style={{ textAlign: "left" }}>
        –
      </td>

      <td style={{ textAlign: "left" }}>
        [Callback API](ref:verifyname-callback) Endpoint.
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_VERIFICATION_AUTHORIZATION_TOKEN\`
      </td>

      <td style={{ textAlign: "left" }}>
        –
      </td>

      <td style={{ textAlign: "left" }}>
        VASP API 호출시 사용할 인증 token 값.
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_VERIFICATION_AUTHORIZATION_KEY\`
      </td>

      <td style={{ textAlign: "left" }}>
        –
      </td>

      <td style={{ textAlign: "left" }}>
        VASP API 호출시 인증 헤더 Key 값. 미설정시 \`Authorization\` 헤더 사용.
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_API_ENDPOINT\`
      </td>

      <td style={{ textAlign: "left" }}>
        –
      </td>

      <td style={{ textAlign: "left" }}>
        VerifyVASP Central API Server Endpoint.

        * PRD (KR): \`https://api-kr.vega-protocol.com\`
        * PRD (Global): \`https://api.vega-protocol.com\`
        * STG (KR): \`https://api-kr.vega-protocol.xyz\`
        * STG (Global): \`https://api.vega-protocol.xyz\`
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_LOG_LEVEL\`
      </td>

      <td style={{ textAlign: "left" }}>
        \`info\`
      </td>

      <td style={{ textAlign: "left" }}>
        Log level.\
        **사용 가능한 값**:\`none\`, \`error\`, \`warn\`, \`info\`, \`debug\`
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_PUBLIC_KEY_TTL\`
      </td>

      <td style={{ textAlign: "left" }}>
        \`1800000\`
      </td>

      <td style={{ textAlign: "left" }}>
        상대 VASP의 공개키 캐싱 TTL(ms).\
        기본 1800000 (30분), 최소 600000 (10분)
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_DECRYPT_API_ENDPOINT\`
      </td>

      <td style={{ textAlign: "left" }}>
        –
      </td>

      <td style={{ textAlign: "left" }}>
        Enclave 데이터베이스 암호화 [대칭키 조회를 위한 API](ref:verifyname-get-decrypted-enckey) Path.
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        \`VEGA_ENCRYPTION_KEY_BASE64\`
      </td>

      <td style={{ textAlign: "left" }}>
        –
      </td>

      <td style={{ textAlign: "left" }}>
        Enclave 데이터베이스 암호화 대칭키 값 또는 API 사용을 위한 참조값.\
        **관련 링크**: [Database Management API](ref:verifyname-get-decrypted-enckey)
      </td>
    </tr>
  </tbody>
</Table>
`}</HTMLBlock>

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
        Enclave 서버 포트 번호.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_ENCLAVE_MODE`
      </td>

      <td>

      </td>

      <td>
        Enclave의 구동모드.
        Enclave를 통해 지원하고자 하는 VerifyVASP의 프로토콜들을 명시합니다.

        단일 또는 복수 설정이 가능하며, 복수 설정시 모드 값을 콤마(,)로 구분하여 모두 입력합니다. 복수 설정시 설정된 모드들을 동시 지원합니다.

        **사용 가능한 모드 값** : `TR`, `VN2`, `VN1_CALL`, `VN1_RESPONSE`

        (ex) `VEGA_ENCLAVE_MODE=TR,VN2`
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
        Enclave 서버의 Public Endpoint URL.\
        VerifyVASP 중앙서버로부터 접근 가능한HTTPS 주소를 입력해야합니다.

        `https://api.vasp.com/enclave`
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
        VerifyVASP API Access Key.\
        온보딩 시 발급받은 값을 입력합니다.

        **관련 링크**: [Onboarding](ref:onboarding-copy-1)
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
        VerifyVASP API Secret Key.\
        온보딩 시 발급받은 값을 입력합니다.

        **관련 링크**: [Onboarding](ref:onboarding-copy-1)
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
        Enclave 데이터베이스의 DBMS 유형.\
        **사용 가능한 값**: `pg`, `mysql`, `mysql2`, `oracledb`, `mssql`

        **관련 링크**: [Enclave Database Setup](ref:database-setup-copy)
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
        데이터베이스 연동을 위한 Host URL.
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
        데이터베이스 포트 번호.
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
        데이터베이스 DB 이름.
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
        Custom schema 이름(PostgreSQL 케이스에 한함).
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
        최소 데이터베이스 Connection Pool 크기.
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
        최대 데이터베이스 Connection Pool 크기.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_DATABASE_SSL`
      </td>

      <td>
        `false`
      </td>

      <td>
        데이터베이스 SSL 연결 사용 여부\
        (사용시 `true`로 설정)
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
        데이터베이스 SSL 연결을 위한 CA 인증서 파일 위치
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
        데이터베이스 SSL 연결을 위한 클라이언트 SSL 인증서 Private Key 경로.
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
        데이터베이스 SSL 연결을 위한 클라이언트 SSL 인증서 경로.
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
        데이터베이스 SSL 연결을 위한 CA 인증서 디렉토리 경로
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
        데이터베이스 SSL 연결을 위한 암호화 방식
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_DATABASE_SSL_REJECT_UNAUTHORIZED`
      </td>

      <td>
        `true`
      </td>

      <td>
        데이터베이스 SSL 연결시 인증서 인증 오류시 연결 거절 여부 (거절시 true로 설정)
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_VERIFICATION_VERIFY_NAME_V2_API_PATH`
      </td>

      <td>

      </td>

      <td>
        `VerifyName`\
        VASP API 호출을 위한 [Verify Name API](ref:verifyname-request-verification) Endpoint.
      </td>
    </tr>

    <tr>
      <td>
        `VEGA_VERIFICATION_VERIFY_NAME_  
                TRANSACTION_API_PATH`
      </td>

      <td>

      </td>

      <td>
        `VerifyName`\
        VASP API 호출을 위한 VerifyName [Check Transaction Status API](ref:verifyname-transaction) Endpoint.
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
        [Callback API](ref:verifyname-callback) Endpoint.
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
        VASP API 호출시 사용할 인증 token 값.
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
        VASP API 호출시 인증 헤더 Key 값. 미설정시 `Authorization` 헤더 사용.
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
        VerifyVASP Central API Server Endpoint.

        * PRD (KR): `https://api-kr.vega-protocol.com`
        * PRD (Global): `https://api.vega-protocol.com`
        * STG (KR): `https://api-kr.vega-protocol.xyz`
        * STG (Global): `https://api.vega-protocol.xyz`
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
        Log level.\
        **사용 가능한 값**:`none`, `error`, `warn`, `info`, `debug`
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
        상대 VASP의 공개키 캐싱 TTL(ms).\
        기본 1800000 (30분), 최소 600000 (10분)
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
        Enclave 데이터베이스 암호화 [대칭키 조회를 위한 API](ref:verifyname-get-decrypted-enckey) Path.
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
        Enclave 데이터베이스 암호화 대칭키 값 또는 API 사용을 위한 참조값.\
        **관련 링크**: [Database Management API](ref:verifyname-get-decrypted-enckey)
      </td>
    </tr>
  </tbody>
</Table>

<br />

## Step 3. 네트워크 설정

Enclave 서버가 정상적으로 실행된 이후, VerifyVASP Central 서버와의 안전한 통신을 위해 네트워크 Allowlist를 구성해야 합니다.

### Enclave 서버 IP를 VerifyVASP 중앙 서버에 등록

VASP의 Enclave 서버 IP를 [support@verifyvasp.com](mailto:support@verifyvasp.com) 으로 전달하여 VerifyVASP측 네트워크 허용 목록에 등록하십시오. 등록 가능한 값은 IP 주소에 한정되며, 도메인 주소는 허용되지 않습니다.

### VerifyVASP 중앙 서버의 IP를 VASP 측에 등록

VerifyVASP Central 서버가 VASP의 인프라에 접근할 수 있도록, VASP 인프라 네트워크 설정으로 VerifyVASP의 IP 주소를 허용 목록에 등록해야 합니다. 해당 IP 정보는 [support@verifyvasp.com](mailto:support@verifyvasp.com) 또는 전용 Slack 채널을 통해 문의해 주세요.

<br />

## Step 4. Enclave 통합 API 구성

Enclave 서버가 정상적으로 구동되어 VerifyVASP Central 서버와 정상적으로 연동되었다면, 이제 Enclave API를 VASP 백엔드에서 호출하여 서비스 및 입출금 프로세스에 VerifyName을 이용한 검증 프로세스를 추가할 수 있습니다.

VerifyName의 API Flow와 이 문서 하위에 포함된 아래 API 명세를 확인하여 Enclave API를 연동하십시오.