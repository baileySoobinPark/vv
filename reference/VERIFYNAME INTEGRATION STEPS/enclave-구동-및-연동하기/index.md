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
        Enclave operating mode. Specifies the VerifyVASP protocols that the Enclave should run. Multiple modes can be specified for concurrent execution.<br><br>
        <b>Available modes</b> : <code>TR</code>, <code>VN2</code>, <code>VN1_CALL</code>, <code>VN1_RESPONSE</code><br><br>
        (ex) <code>VEGA_ENCLAVE_MODE=TR,VN2</code>
      </td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_ENCLAVE_PUBLIC_ENDPOINT</code></td>
      <td>–</td>
      <td>
        Public endpoint URL for the Enclave server.<br>
        Enter the URL used for direct access from the VerifyVASP central server.<br><br>

        <code>https://api.vasp.com/enclave</code>
      </td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_ALLIANCE_ACCESS_KEY</code></td>
      <td>–</td>
      <td>
        VerifyVASP API Access Key.<br>
        Enter the issued key provided during onboarding.<br><br>

        <b>link</b>: <a href="/reference/onboarding-copy">Onboarding</a>
      </td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_ALLIANCE_SECRET_KEY</code></td>
      <td>–</td>
      <td>
        VerifyVASP API Secret Key.<br>
        Enter the issued key provided during onboarding.<br><br>

        <b>link</b>: <a href="/reference/onboarding-copy">Onboarding</a>
      </td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_CLIENT</code></td>
      <td><code>mysql2</code></td>
      <td>
        Type of DBMS for the Enclave database.<br>
        <b>Support values</b>: <code>pg</code>, <code>mysql</code>, <code>mysql2</code>, <code>oracledb</code>, <code>mssql</code><br><br>

        <b>link</b>: <a href="/reference/enclave-데이터베이스-생성">Enclave Database Setup</a>
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
      <td>Host URL for database connection.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_PORT</code></td>
      <td><code>3306</code></td>
      <td>Database port number.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_DB</code></td>
      <td><code>verifyvasp</code></td>
      <td>Database name.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_SEARCH_PATH</code></td>
      <td><code>enclave</code></td>
      <td>Custom schema name (PostgreSQL only).</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_POOL_MIN</code></td>
      <td><code>0</code></td>
      <td>Minimum database connection pool size.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_POOL_MAX</code></td>
      <td><code>5</code></td>
      <td>Maximum database connection pool size.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_SSL</code></td>
      <td><code>false</code></td>
      <td>
        Whether to use SSL for database connection<br>
        (<code>true or false</code>)
      </td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_SSL_CA</code></td>
      <td>–</td>
      <td>CA certificate for database SSL connection</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_SSL_KEY</code></td>
      <td>–</td>
      <td>Private key file for database SSL connection.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_SSL_CERT</code></td>
      <td>–</td>
      <td>Client certificate for database SSL connection.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_SSL_CAPATH</code></td>
      <td>–</td>
      <td>Path to CA certificates for database SSL connection</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_SSL_CIPHER</code></td>
      <td>–</td>
      <td>Cipher suites for database SSL connection</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DATABASE_SSL_REJECT_UNAUTHORIZED</code></td>
      <td><code>true</code></td>
      <td>Whether to reject unauthorized SSL certificates (true or false)</td>
    </tr>

    <tr>
      <td class="code-col"><code class="long-var2">VEGA_VERIFICATION_VERIFY_NAME_V2_API_PATH</code></td>
      <td></td>
      <td>
        <a href="/reference/verifyname-request-verification">Verify Name API</a> endpoint path.
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
      <td>Pre-issued authentication token for calling VASP API.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_VERIFICATION_AUTHORIZATION_KEY</code></td>
      <td>–</td>
      <td>Header key used for API authentication. Default is <code>Authorization</code> if not set.</td>
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
        <b>Available values</b>:<code>none</code>, <code>error</code>, <code>warn</code>, <code>info</code>, <code>debug</code>
      </td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_PUBLIC_KEY_TTL</code></td>
      <td><code>1800000</code></td>
      <td>
        Public key cache TTL in milliseconds.<br>
        (default: 1800000, min: 600000
      </td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_DECRYPT_API_ENDPOINT</code></td>
      <td>–</td>
      <td>API endpoint for retrieving the database encryption key.</td>
    </tr>

    <tr>
      <td class="code-col"><code>VEGA_ENCRYPTION_KEY_BASE64</code></td>
      <td>–</td>
      <td>
        Reference value for the database encryption key.
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