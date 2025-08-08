---
title: Running and Integrating the Enclave
excerpt: >-
  Once the Enclave database has been configured, you can proceed to download the
  Enclave Docker image and start the server. This document provides guidance on
  running the Enclave and configuring its environment variables.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Step 1. Prepare the Enclave Docker Image

The VerifyVASP Enclave server is distributed as a Docker image via the AWS ECR registry. To obtain access, please send the email address for which you will receive the AWS CLI Access Key to [support@verifyvasp.com](mailto:support@verifyvasp.com) .

<Enclave />

<br />

## Step 2. Start the Enclave Server and Configure Environment Variables

Once access is granted, download and run the Enclave server within your internal VASP infrastructure.\
Before starting the server, you must configure the required environment variables to ensure seamless communication between the Enclave and its related components.

The Enclave environment variables are categorized into the following five main groups:

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
      <th>Environment Variable Group</th>
      <th>Description</th>
      <th>Example</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Server and Database Configuration Variables</td>
      <td>Settings required for server operation and database access</td>
      <td>Server endpoint/port, DB username/password, DB connection settings, etc.</td>
    </tr>
    <tr>
      <td>Authentication Variables</td>
      <td>Authentication details for connecting to the VerifyVASP central server and third-party services</td>
      <td>Access key / secret key</td>
    </tr>
    <tr>
      <td>VASP API Endpoints</td>
      <td>API endpoints used by the Enclave when calling VASP APIs</td>
      <td>Owner verification (VerifyName) API endpoint</td>
    </tr>
    <tr>
      <td>Security and Configuration Variables</td>
      <td>Variables for defining the security level and operational settings of the Enclave server</td>
      <td>Public key caching options</td>
    </tr>
    <tr>
      <td>Enclave Mode Settings</td>
      <td>Operation mode of the Enclave and protocol-specific configurations</td>
      <td>TR(TravelRule), VN1_CALL/VN1_RESPONSE(VerifyName 1.0), VN2</td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

The following table summarizes the purpose, default values, and configuration methods for the environment variables. **Do not modify the environment variable names**; they must be used exactly as provided.

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
  padding: 8px;
  vertical-align: top;
  word-break: break-word;
}

.enclave-env-table th {
  text-align: left;
  font-weight: bold;
  background-color: #f0f0f0;
}
  
.enclave-env-table td.code-col {
  max-width: 300px;
  overflow: hidden;
  white-space: nowrap;
}

.enclave-env-table td.code-col code.long-var {
  letter-spacing: -0.55px;
  transform: scaleX(0.83);
  transform-origin: left center;
  display: inline-block;
  vertical-align: baseline;
  margin: 0;
  padding: 1;
}

.enclave-env-table td.code-col code.long-var2 {
  letter-spacing: -0.4px;
  transform: scaleX(0.93);
  transform-origin: left center;
  display: inline-block;
  vertical-align: baseline;
  margin: 0;
  padding: 1;
}
</style>
<table class="enclave-env-table">
  <thead>
    <tr>
      <th width=350px>Variable Name</th>
      <th width=120px>Default Value</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td class="code-col"><code>VEGA_SERVER_PORT</code></td>
      <td>21117</td>
      <td>Enclave server port number.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_ENCLAVE_MODE</code></td>
      <td></td>
      <td>
        Enclave operating mode.<br>
        Specifies the VerifyVASP protocols that the Enclave should run.<br><br>
        단일 또는 복수 설정이 가능하며, 복수 설정시 모드 값을 콤마(,)로 구분하여 모두 입력합니다. 복수 설정시 설정된 모드들을 동시 지원합니다.<br><br>
        <b>사용 가능한 모드 값</b> : <code>TR</code>, <code>VN2</code>, <code>VN1_CALL</code>, <code>VN1_RESPONSE</code><br><br>
        (ex) <code>VEGA_ENCLAVE_MODE=TR,VN2</code>
      </td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_ENCLAVE_PUBLIC_ENDPOINT</code></td>
      <td>–</td>
      <td>
        Enclave 서버의 Public Endpoint URL.<br>
        VerifyVASP 중앙서버로부터 접근 가능한HTTPS 주소를 입력해야합니다.<br><br>

        <code>https://api.vasp.com/enclave</code>
      </td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_ALLIANCE_ACCESS_KEY</code></td>
      <td>–</td>
      <td>
        VerifyVASP API Access Key.<br>
        온보딩 시 발급받은 값을 입력합니다.<br><br>

        <b>관련 링크</b>: <a href="/reference/onboarding-copy">Onboarding</a>
      </td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_ALLIANCE_SECRET_KEY</code></td>
      <td>–</td>
      <td>
        VerifyVASP API Secret Key.<br>
        온보딩 시 발급받은 값을 입력합니다.<br><br>

        <b>관련 링크</b>: <a href="/reference/onboarding-copy">Onboarding</a>
      </td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_CLIENT</code></td>
      <td><code>mysql2</code></td>
      <td>
        Enclave 데이터베이스의 DBMS 유형.<br>
        <b>사용 가능한 값</b>: <code>pg</code>, <code>mysql</code>, <code>mysql2</code>, <code>oracledb</code>, <code>mssql</code><br><br>

        <b>관련 링크</b>: <a href="/reference/enclave-데이터베이스-생성">Enclave Database Setup</a>
      </td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_USERNAME</code></td>
      <td>–</td>
      <td>Database user name.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_PASSWORD</code></td>
      <td>–</td>
      <td>Database password.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_HOST</code></td>
      <td>–</td>
      <td>데이터베이스 연동을 위한 Host URL.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_PORT</code></td>
      <td><code>3306</code></td>
      <td>데이터베이스 포트 번호.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_DB</code></td>
      <td><code>verifyvasp</code></td>
      <td>데이터베이스 DB 이름.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_SEARCH_PATH</code></td>
      <td><code>enclave</code></td>
      <td>Custom schema 이름(PostgreSQL 케이스에 한함).</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_POOL_MIN</code></td>
      <td><code>0</code></td>
      <td>최소 데이터베이스 Connection Pool 크기.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_POOL_MAX</code></td>
      <td><code>5</code></td>
      <td>최대 데이터베이스 Connection Pool 크기.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_SSL</code></td>
      <td><code>false</code></td>
      <td>
        데이터베이스 SSL 연결 사용 여부<br>
        (사용시 <code>true</code>로 설정)
      </td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_SSL_CA</code></td>
      <td>–</td>
      <td>데이터베이스 SSL 연결을 위한 CA 인증서 파일 위치</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_SSL_KEY</code></td>
      <td>–</td>
      <td>데이터베이스 SSL 연결을 위한 클라이언트 SSL 인증서 Private Key 경로.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_SSL_CERT</code></td>
      <td>–</td>
      <td>데이터베이스 SSL 연결을 위한 클라이언트 SSL 인증서 경로.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_SSL_CAPATH</code></td>
      <td>–</td>
      <td>데이터베이스 SSL 연결을 위한 CA 인증서 디렉토리 경로</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_SSL_CIPHER</code></td>
      <td>–</td>
      <td>데이터베이스 SSL 연결을 위한 암호화 방식</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_SSL_REJECT_UNAUTHORIZED</code></td>
      <td><code>true</code></td>
      <td>데이터베이스 SSL 연결시 인증서 인증 오류시 연결 거절 여부 (거절시 true로 설정)</td>
    </tr>

    <tr>
      <td class="code-col"><code class="long-var2">VEGA_VERIFICATION_VERIFY_NAME_V2_API_PATH</code></td>
      <td></td>
      <td>
        <code>VerifyName</code> VASP API 호출을 위한 <a href="/reference/verifyname-request-verification">Verify Name API</a> Endpoint.
      </td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_VERIFICATION_CALLBACK_API_PATH</code></td>
      <td>–</td>
      <td><a href="/reference/verifyname-callback">Callback API</a> Endpoint.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_VERIFICATION_AUTHORIZATION_TOKEN</code></td>
      <td>–</td>
      <td>VASP API 호출시 사용할 인증 token 값.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_VERIFICATION_AUTHORIZATION_KEY</code></td>
      <td>–</td>
      <td>VASP API 호출시 인증 헤더 Key 값. 미설정시 <code>Authorization</code> 헤더 사용.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_API_ENDPOINT</code></td>
      <td>–</td>
      <td>
        VerifyVASP Central API Server Endpoint.<br><br>

        <li>PRD (KR): <code>https://api-kr.vega-protocol.com</code></li>
        <li>PRD (Global): <code>https://api.vega-protocol.com</code></li>
        <li>STG (KR): <code>https://api-kr.vega-protocol.xyz</code></li>
        <li>STG (Global): <code>https://api.vega-protocol.xyz</code></li>
      </td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_LOG_LEVEL</code></td>
      <td><code>info</code></td>
      <td>
        Log level.<br>
        <b>사용 가능한 값</b>:<code>none</code>, <code>error</code>, <code>warn</code>, <code>info</code>, <code>debug</code>
      </td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_PUBLIC_KEY_TTL</code></td>
      <td><code>1800000</code></td>
      <td>
        상대 VASP의 공개키 캐싱 TTL(ms).<br>
        기본 1800000 (30분), 최소 600000 (10분)
      </td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DECRYPT_API_ENDPOINT</code></td>
      <td>–</td>
      <td>Enclave 데이터베이스 암호화 <a href="/reference/verifyname-get-decrypted-enckey">대칭키 조회를 위한 API</a> Path.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_ENCRYPTION_KEY_BASE64</code></td>
      <td>–</td>
      <td>
        Enclave 데이터베이스 암호화 대칭키 값 또는 API 사용을 위한 참조값.<br>
        <b>관련 링크</b>: <a href="/reference/verifyname-get-decrypted-enckey">Database Management API</a>
      </td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

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