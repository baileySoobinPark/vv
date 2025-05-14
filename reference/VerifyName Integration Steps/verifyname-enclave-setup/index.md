---
title: Enclave Setup
deprecated: false
hidden: true
metadata:
  robots: index
---
## **Preparation Enclave Docker Image**

The enclave server, developed by VerifyVASP, is distributed as a Docker image via a private Docker Hub registry. To gain download access, send your Docker Hub ID to **[corporate@verifyvasp.com](mailto:corporate@verifyvasp.com).**

## **Running Enclave Server with Environment Variables Configuration**

After obtaining access to the Enclave Docker image, download and deploy the Enclave server within your VASP's infrastructure. Before starting the server, configure the required environment variables to enable seamless communication between the Enclave server and related components.

Enclave environment variables can be categorized into four main groups:

* **Server and Database Configuration Variables**
  * Parameters required for server operation and database connectivity.
  * ex) Enclave server endpoint/port, DB user name/password, DB connection config...
* **Authentication Credentials**
  * Credentials for connecting to the VerifyVASP central server and utilizing third-party services.
  * ex) Alliance access key/secret key, Chainanalysis API key, Refinitiv API key...
* **VASP API Endpoints**
  * Information for VASP implemented API endpoints used by the Enclave to call VASP APIs.
  * ex) Verify User Account API Endpoint, Verify User API Endpoint...
* **Security and Preferences Settings**
  * Variables to configure security settings and operational preferences for the Enclave server.
  * ex) Public key caching options
* **Enclave Modes**
  * The following values can be used for the Enclave mode.
    * TR
    * VN1\_CALL (This mode will be deprecated soon.)
    * VN1\_RESPONSE (This mode will be deprecated soon.)
    * VN2
  * These values must be set in the `VEGA_ENCLAVE_MODE` field of the environment variables. At least one mode must be specified, and multiple modes can be entered by separating them with commas.
  * Example) `VEGA_ENCLAVE_MODE`=TR,VN2,VN1\_CALL,VN1\_RESPONSE
    * When only TR is set: This means that your VASP is running the enclave server for handling TravelRule API requests and responses.
    * When only VN1\_CALL is set: This means that your VASP is running the enclave server for handling VerifyName V1 API requests.
    * When only VN1\_RESPONSE is set: This means that your VASP is running the enclave server for handling VerifyName V1 API responses.
    * When only VN2 is set: This means that your VASP is running the enclave server for handling VerifyName V2 API requests and responses.
    * 콤마를 구분자로 복수개의 모드를 설정한 경우: 설정한 모드별 기능을 모두 사용하겠다는 의미입니다.

The following table summarizes all essential environment variables, providing details on their purpose, default values, and how to configure them effectively.

**`The names of the environment variables must never be changed.`**

<br />

### Environment Variables

<Table align={["left","left","left"]}>
  <thead>
    <tr>
      <th>
        Variable
      </th>

      <th>
        Default
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        VEGA\_SERVER\_PORT
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
        VEGA\_ENCLAVE\_MODE
      </td>

      <td>

      </td>

      <td>
        사용 목적 별 모드 설정 입니다. 아래 모드 중 선택(최소 한개 이상)하여 콤마를 구분자로 하여 설정 하시면 됩니다.

        설정 가능한 모드:
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

### **Network Configuration**

Once the Enclave server is successfully running, configure network allowlisting with the VerifyVASP Central Server to ensure secure communication.

### **Register Enclave Server IP to VerifyVASP Central Server's Allowlist**

* Please send the IP address of Your VASP Enclave server to **[corporate@verifyvasp.com](mailto:corporate@verifyvasp.com)**, the IP will be whitelisted by VerifyVASP.
* Only IP addresses can be registered; domain addresses are not acceptable.

### **Register VerifyVASP Central Server IP on VASP**

* Your VASP needs to whitelist Central Server IP address of VerifyVASP to get access to VerifyVASP network. For information on IP address, please contact **[corporate@verifyvasp.com](mailto:corporate@verifyvasp.com)** or the support team via your dedicated Slack channel.
* You will receive a central server IP address of VerifyVASP to be allowed to access to VASP infrastructures.

<br />

> ⚠️ The names of the environment variables must never be changed.

## Environment Variables

| Environment Name                                     | Default | Description                                                                 |
| ---------------------------------------------------- | ------- | --------------------------------------------------------------------------- |
| `VEGA_VERIFICATION_VERIFY_NAME_V2_API_PATH`          |         | Owner Verification API (VASP API) Endpoint URL.                             |
| `VEGA_VERIFICATION_VERIFY_NAME_TRANSACTION_API_PATH` |         | Transaction State Query API (VASP API) Endpoint URL for Owner Verification. |

<br />

## **Enclave API Integration**

Now, it's time to integrate the VerifyName into your VASP's withdrawal process.

For detailed API specifications, refer to following Enclave API References.

Once the integration of Enclave API calls is complete, proceed to the testing phase to ensure seamless communication between both endpoints.

[VASP Management API](ref:verifyname-get-Vasp-Id)

[Verification API](ref:verifyname-owner-verification)

[Transaction API](ref:verifyname-check-transaction-status)

[Report API](ref:verifyname-report-transaction-result-or-error)