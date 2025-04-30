---
title: Enclave Installation
deprecated: false
hidden: true
metadata:
  robots: index
---
### **Preparation Enclave Docker Image**

The enclave server, developed by VerifyVASP, is distributed as a Docker image via a private Docker Hub registry. To gain download access, send your Docker Hub ID to **[corporate@verifyvasp.com](mailto:corporate@verifyvasp.com).**

### **Running Enclave Server with Environment Variables Configuration**

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
* Enclave Modes
  * enclave mode 에는 TR, VN1\_CALL, VN1\_RESPONSE, VN2 가 있습니다. 이 값들은 enclave 환경변수 `VEGA_ENCLAVE_MODE` 에 최소 한개 이상 콤마를 구분자로 하여 설정 되어야 합니다.
  * 예시) VEGA\_ENCLAVE\_MODE=TR,VN2,VN1\_CALL,VN1\_RESPONSE
    * TR 을 설정한 경우: TravelRule API 를 호출/응답 하기 위한 목적으로 enclave 를 기동한다는 의미입니다.
    * VN2 를 설정한 경우: VerifyName V2 API 를 호출/응답 하기 위한 목적으로 enclave 를 기동한다는 의미입니다.
    * VN1\_CALL 을 설정한 경우: VerifyName V1 API 를 호출 하기 위한 목적으로 enclave 를 기동한다는 의미입니다. (deprecate 예정)
    * VN1\_RESPONSE 를 설정한 경우: VerifyName V2 API 를 응답하기 위한 목적으로 enclave 를 기동한다는 의미입니다. (deprecate 예정)
    * 콤마를 구분자로 복수개의 모드를 설정한 경우: 설정한 모드별 기능을 모두 사용하겠다는 의미입니다.

The following table summarizes all essential environment variables, providing details on their purpose, default values, and how to configure them effectively.

**`The names of the environment variables must never be changed.`**

| Environment Name    | Default | Description                                                       |
| ------------------- | ------- | ----------------------------------------------------------------- |
| VEGA\_SERVER\_PORT  | 21117   | Enclave Server Port Number.                                       |
| VEGA\_ENCLAVE\_MODE |         | 사용 목적 별 모드 설정 입니다. 아래 모드 중 선택(최소 한개 이상)하여 콤마를 구분자로 하여 설정 하시면 됩니다. |

설정 가능한 모드:\
`TR` `VN2` `VN1_CALL` `VN1_RESPONSE` |
\| VEGA\_ENCLAVE\_PUBLIC\_ENDPOINT |  | Public Endpoint URL for VASP's Enclave Server, accessible by VerifyVASP Central Server. Must use the HTTPS protocol. Example: “[https://api.vasp.com/enclave”](https://api.vasp.com/enclave%E2%80%9D) |
\| VEGA\_ALLIANCE\_ACCESS\_KEY |  | The API Access Key issued via VerifyVASP Console. |
\| VEGA\_ALLIANCE\_SECRET\_KEY |  | The API Secret Key issued via VerifyVASP Console. |
\| VEGA\_DATABASE\_CLIENT | mysql2 | Type of the database. Acceptable values: pg, mysql, mysql2, oracledb, mssql |
\| VEGA\_DATABASE\_USERNAME |  | Database user name. |
\| VEGA\_DATABASE\_PASSWORD |  | Database password. |
\| VEGA\_DATABASE\_HOST |  | Address to access the database. |
\| VEGA\_DATABASE\_PORT | 3306 | Port number to access the database. |
\| VEGA\_DATABASE\_DB | verifyvasp | Database name that the Enclave server uses. |
\| VEGA\_DATABASE\_SEARCH\_PATH | enclave | Custom schema name for PostgreSQL. |
\| VEGA\_DATABASE\_POOL\_MIN | 0 | Minimum value of database connection pool. |
\| VEGA\_DATABASE\_POOL\_MAX | 5 | Maximum value of database connection pool. |
\| VEGA\_DATABASE\_SSL | true / false | SSL Connection to Database (Enabled/Disabled). |
\| VEGA\_DATABASE\_SSL\_CA |  | Path to CA Certificate File for Server Authentication. |
\| VEGA\_DATABASE\_SSL\_KEY |  | Path to Client Certificate Private Key File for SSL Connection. |
\| VEGA\_DATABASE\_SSL\_CERT |  | Path to Client Public Certificate File for SSL Connection. |
\| VEGA\_DATABASE\_SSL\_CAPATH |  | Directory Path that Stores CA Certificates. |
\| VEGA\_DATABASE\_SSL\_CIPHER |  | Encryption Algorithms for Client-Server Communication. |
\| VEGA\_DATABASE\_SSL\_REJECT\_UNAUTHORIZED | true / false | Allow SSL Connection Even if Server Certificate is Untrusted. |
\| VEGA\_VERIFICATION\_CALLBACK\_API\_PATH |  | Callback API (VASP API) Endpoint URL. |
\| VEGA\_VERIFICATION\_AUTHORIZATION\_TOKEN |  | The authorization token value for Enclave to VASP’s Backend API Calls. Sent via header when Enclave server calls VASP API. |
\| VEGA\_VERIFICATION\_AUTHORIZATION\_KEY |  | The authorization header key for delivering the token. If not set, uses `Authorization` header. |
\| VEGA\_API\_ENDPOINT |  | VerifyVASP Central API Server Address. PRD (KR): [https://api-kr.vega-protocol.com](https://api-kr.vega-protocol.com/), PRD (Global): [https://api.vega-protocol.com](https://api.vega-protocol.com/), STG (KR): [https://api-kr.vega-protocol.xyz](https://api-kr.vega-protocol.xyz/), STG (Global): [https://api.vega-protocol.xyz](https://api.vega-protocol.xyz/) |
\| VEGA\_LOG\_LEVEL | info | Log Level. Acceptable: none, error, warn, info, debug |
\| VEGA\_PUBLIC\_KEY\_TTL | 1800000 | Cache duration for public key of counterparty VASP (in ms). Default: 1800000 (30 min). Min: 600000 (10 min) |
\| VEGA\_DECRYPT\_API\_ENDPOINT |  | Endpoint of external service for decrypting VEGA\_ENCRYPTION\_KEY\_BASE64. |
\| VEGA\_ENCRYPTION\_KEY\_BASE64 |  | Encryption Key for Personal Data. Used directly or sent to VEGA\_DECRYPT\_API\_ENDPOINT to retrieve actual key. |

### **Network Configuration**

Once the Enclave server is successfully running, configure network allowlisting with the VerifyVASP Central Server to ensure secure communication.

### **Register Enclave Server IP to VerifyVASP Central Server's Allowlist**

* Please send the IP address of Your VASP Enclave server to **[corporate@verifyvasp.com](mailto:corporate@verifyvasp.com)**, the IP will be whitelisted by VerifyVASP.
* Only IP addresses can be registered; domain addresses are not acceptable.

### **Register VerifyVASP Central Server IP on VASP**

* Your VASP needs to whitelist Central Server IP address of VerifyVASP to get access to VerifyVASP network. For information on IP address, please contact **[corporate@verifyvasp.com](mailto:corporate@verifyvasp.com)** or the support team via your dedicated Slack channel.
* You will receive a central server IP address of VerifyVASP to be allowed to access to VASP infrastructures.