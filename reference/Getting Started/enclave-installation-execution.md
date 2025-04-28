---
title: Enclave Installation & Execution
excerpt: Setting up the environment of the enclave server provided by VerifyVASP.
deprecated: false
hidden: true
metadata:
  robots: index
---
<Image align="center" src="https://files.readme.io/f42d76b7f747b877b9e306f817e65822e61080553223a443e9c0530c04b1e63f-VV_Architecture3.png" />

<br />

## System Requirements

* Structure of the system/infrastructure
* Enclave Server
  * CPU: Minimum 2G, 4G recommended
  * Memory: Minimum 2G, 4G recommended
  * Storage: Minimum 8G, 16G recommended (including the docker and container)

<br />

## Installing Enclave Server

* Download enclave docker image
  * Enclave docker image is in Private Registry. You can acquire access by delivering a Docker Hub Credential ID to the admin.
  * A VASP can download a Docker image by first requesting access from VV admin manager ([corporate@verifyvasp.com](mailto:corporate@verifyvasp.com)).
  * VASPs would require VASP’s Docker Hub credential (ID) to provide access. Once access is granted, they can follow the instruction.
  * Download
    ```shell
    docker pull verifyvasp/enclave:v1.5.7
    ```

<br />

## Enclave Server Environment Variable

| Environment Variable                       | Default    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------ | ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| VEGA\_SERVER\_PORT                         | 21117      | Port no. of the enclave server.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| VEGA\_ENCLAVE\_PUBLIC\_ENDPOINT            |            | Public endpoint URL of the enclave server accessible from VV central server. Must use HTTPS protocol. \<br> e.g. “[https://api.vasp.com/enclave”](https://api.vasp.com/enclave”)                                                                                                                                                                                                                                                                                                                                              |
| VEGA\_ALLIANCE\_ACCESS\_KEY                |            | accessKey part of the API-Key                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| VEGA\_ALLIANCE\_SECRET\_KEY                |            | secretKey part of the API-Key                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| VEGA\_DATABASE\_CLIENT                     | mysql2     | A type of database. \<br> An appropriate value for a database client is to be entered. pg, mysql, mysql2, oracledb, mssql values can be used.                                                                                                                                                                                                                                                                                                                                                                                 |
| VEGA\_DATABASE\_USERNAME                   |            | Database user name                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| VEGA\_DATABASE\_PASSWORD                   |            | Database password                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| VEGA\_DATABASE\_HOST                       |            | Address to access database                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| VEGA\_DATABASE\_PORT                       | 3306       | Port number to access database                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| VEGA\_DATABASE\_DB                         | verifyvasp | Database name that enclave server uses                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| VEGA\_DATABASE\_SEARCH\_PATH               | enclave    | Custom schema name for PostgreSQL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| VEGA\_DATABASE\_POOL\_MIN                  | 0          | Minimum value of database connection pool                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| VEGA\_DATABASE\_POOL\_MAX                  | 5          | Maximum value of database connection pool                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| VEGA\_DATABASE\_SSL                        |            | Whether to use SSL connection for the database (true or false)                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| VEGA\_DATABASE\_SSL\_CA                    |            | Path to the Certificate Authority (CA) certificate file for verifying the server's certificate                                                                                                                                                                                                                                                                                                                                                                                                                                |
| VEGA\_DATABASE\_SSL\_KEY                   |            | Path to the client private key file for the SSL connection                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| VEGA\_DATABASE\_SSL\_CERT                  |            | Path to the client public certificate file for the SSL connection                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| VEGA\_DATABASE\_SSL\_CAPATH                |            | Directory path where CA certificates are stored                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| VEGA\_DATABASE\_SSL\_CIPHER                |            | Specifies the encryption algorithms that can be used between the client and server                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| VEGA\_DATABASE\_SSL\_REJECT\_UNAUTHORIZED  |            | Whether to allow SSL connections even if the server's certificate is not trusted (true or false)                                                                                                                                                                                                                                                                                                                                                                                                                              |
| VEGA\_VERIFICATION\_API\_PATH              |            | User verification API endpoint URL.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| VEGA\_VERIFICATION\_ACCOUNT\_API\_PATH     |            | User account (wallet address and owner name) verification API Endpoint URL.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| VEGA\_VERIFICATION\_TRANSACTION\_API\_PATH |            | API Endpoint URL to check the status of the transaction process.                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| VEGA\_VERIFICATION\_CALLBACK\_API\_PATH    |            | Callback API Endpoint URL to receive the result of asynchronous request.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| VEGA\_VERIFICATION\_AUTHORIZATION\_TOKEN   |            | Verification token to call API with VASP backend. When the enclave server calls API with the VASP backend, verification taken is delivered to the header of VASP Authorization.                                                                                                                                                                                                                                                                                                                                               |
| VEGA\_VERIFICATION\_AUTHORIZATION\_KEY     |            | Header key value to pass the authentication token when making API calls to the VASP backend. If not set, the authentication token is passed through the Authorization header.                                                                                                                                                                                                                                                                                                                                                 |
| VEGA\_API\_ENDPOINT                        |            | VerifyVASP central API server address. As mentioned above, one of the two environments could be used depending on the purpose. In a production environment, register through [https://api.vega-protocol.com](https://api.vega-protocol.com). In a staging environment, register through [https://api.vega-protocol.xyz](https://api.vega-protocol.xyz).                                                                                                                                                                       |
| VEGA\_LOG\_LEVEL                           | info       | You can determine the log level of the enclave server. \<br> none, error, warn, info, debug values can be used.                                                                                                                                                                                                                                                                                                                                                                                                               |
| VEGA\_PUBLIC\_KEY\_TTL                     | 1800000    | The duration to cache the public key of the other VASP which will be used for E2EE can be set up. The unit must be milliseconds.                                                                                                                                                                                                                                                                                                                                                                                              |
| VEGA\_ENCRYPTION\_KEY\_BASE64              |            | Encryption key to be used to encrypt personal information. \<br> Unless the VEGA\_DECRYPT\_API\_ENDPOINT below is entered, the environment value set up in VEGA\_ENCRYPTION\_KEY\_BASE64 will be used as an encryption key of the database (Not Recommended). \<br> If the VEGA\_DECRYPT\_API\_ENDPOINT is set up, the environment value set up in VEGA\_ENCRYPTION\_KEY\_BASE64 will be set up as an inputKey of the request body. Then, the outputKey as a response to calling the API issued is used as an encryption key. |
| VEGA\_DECRYPT\_API\_ENDPOINT               |            | The outside service endpoint to decrypt the encryption key set up in VEGA\_ENCRYPTION\_KEY\_BASE64 once again. It is set up to use the outside service relevant to encryption, such as HSM.                                                                                                                                                                                                                                                                                                                                   |
| VEGA\_CHAINALYSIS\_SANCTION\_API\_KEY      |            | If you want to screen the other party's wallet address using the Chainalysis sanction API integration feature, set the Chainalysis Sanction API Key.                                                                                                                                                                                                                                                                                                                                                                          |
| VEGA\_CHAINALYSIS\_KYT\_API\_KEY           |            | If you want to use the Chainalysis KYT API integration to perform a risk assessment of the other party's wallet address or transfer transaction, set the Chainalysis KYT API Key.                                                                                                                                                                                                                                                                                                                                             |
| VEGA\_REFINITIV\_WCO\_API\_KEY             |            | If you want to use the Refinitiv World Check One API integration to perform risk assessments of the other party's user, set up a Refinitiv World Check One API Key.                                                                                                                                                                                                                                                                                                                                                           |
| VEGA\_REFINITIV\_WCO\_API\_SECRET          |            | If you want to use the Refinitiv World Check One API integration to perform risk assessments of the other party's user, set the secret issued with the Refinitiv World Check One API Key                                                                                                                                                                                                                                                                                                                                      |
| VEGA\_REFINITIV\_WCO\_GROUP\_ID            |            | If you want to use the Refinitiv World Check One API integration to perform risk assessments of the other party's user, set the group ID created in the Refinitiv console site. All screening cases performed through the integration belong to this group.                                                                                                                                                                                                                                                                   |

<br />

## How to Run Enclave

### How to Run by Writing an Environment Value on a File

* Run by writing an environment value on a file. (e.g. stg\_env)

```shell
;Enclave Server
VEGA_SERVER_PORT=21117
VEGA_ENCLAVE_PUBLIC_ENDPOINT=<Enclave Server endpoint URL>

;API Keys
VEGA_ALLIANCE_ACCESS_KEY=<issued accessKey>
VEGA_ALLIANCE_SECRET_KEY=<issued secretKey>

;DB Client
VEGA_DATABASE_CLIENT=mysql2
VEGA_DATABASE_USERNAME=vasp
VEGA_DATABASE_PASSWORD=1234
VEGA_DATABASE_HOST=<database endpoint URL>
VEGA_DATABASE_PORT=3306
VEGA_DATABASE_DB=vega
VEGA_DATABASE_POOL_MIN=0
VEGA_DATABASE_POOL_MAX=5

;VASP API
VEGA_VERIFICATION_API_PATH=<VerifyUser API endpoint URL>
VEGA_VERIFICATION_ACCOUNT_API_PATH=<VerifyAccount API endpoint URL>
VEGA_VERIFICATION_TRANSACTION_API_PATH=<Transaction processing state querying API endpoint URL>
VEGA_VERIFICATION_CALLBACK_API_PATH=<Callback API endpoint URL>
VEGA_VERIFICATION_AUTHORIZATION_TOKEN=<Auth Token>
VEGA_VERIFICATION_AUTHORIZATION_KEY=<Auth Token Header Key>

;VerifyVASP Central Server URL
VEGA_API_ENDPOINT=https://api.vega-protocol.xyz

;Log level
VEGA_LOG_LEVEL=info

;DB Encryption Key
VEGA_DECRYPT_API_ENDPOINT=<HSM URL>
VEGA_ENCRYPTION_KEY_BASE64=<Public Key on base64>

;Caching
VEGA_PUBLIC_KEY_TTL=1800000

;Risk Assessment
VEGA_CHAINALYSIS_SANCTION_API_KEY=<Chainalysis Public Sanction API Key>
VEGA_CHAINALYSIS_KYT_API_KEY=<Chainalysis KYT API Key>
VEGA_REFINITIV_WCO_API_KEY=<Refinitiv World Check One API Key>
VEGA_REFINITIV_WCO_API_SECRET=<Refinitiv World Check One API Secret>
VEGA_REFINITIV_WCO_GROUP_ID=<Refinitiv World Check One Group ID>
```

<br />

* Run

```shell
$ docker run -d -p 21117:21117 --env-file=<Name of the Environment Variable File> --name=enclave verifyvasp/enclave:v1.5.7
```

<br />

## How to Run by Entering an Environment Value as Command

```shell
$ docker run -d -p 21117:21117 \\\\
-e VEGA_SERVER_PORT=21117 \\\\
-e VEGA_ENCLAVE_PUBLIC_ENDPOINT=<Enclave Server endpoint URL> \\\\
-e VEGA_ALLIANCE_ACCESS_KEY=<issued accessKey> \\\\
-e VEGA_ALLIANCE_SECRET_KEY=<issued secretKey> \\\\
-e VEGA_DATABASE_USERNAME=vasp \\\\
-e VEGA_DATABASE_PASSWORD=1234 \\\\
-e VEGA_DATABASE_HOST=<database endpoint URL> \\\\
-e VEGA_DATABASE_PORT=3306 \\\\
-e VEGA_DATABASE_DB=vega \\\\
-e VEGA_VERIFICATION_API_PATH=<VerifyUser API endpoint URL> \\\\
-e VEGA_VERIFICATION_ACCOUNT_API_PATH=<VerifyAccount API endpoint URL> \\\\
-e VEGA_VERIFICATION_TRANSACTION_API_PATH=<Transaction processing state querying API endpoint URL> \\\\
-e VEGA_VERIFICATION_CALLBACK_API_PATH=<Callback API endpoint URL> \\\\
-e VEGA_VERIFICATION_AUTHORIZATION_TOKEN=<Auth Token> \\\\
-e VEGA_VERIFICATION_AUTHORIZATION_KEY=<Auth Token Header Key> \\\\
-e VEGA_API_ENDPOINT=https://api.vega-protocol.xyz \\\\
-e VEGA_LOG_LEVEL=info \\\\
-e VEGA_PUBLIC_KEY_TTL=1800000 \\\\
-e VEGA_DECRYPT_API_ENDPOINT=<HSM URL> \\\\
-e VEGA_ENCRYPTION_KEY_BASE64=<Public Key on base64> \\\\
-e VEGA_CHAINALYSIS_SANCTION_API_KEY=<Chainalysis Public Sanction API Key> \\\\
-e VEGA_CHAINALYSIS_KYT_API_KEY=<Chainalysis KYT API Key> \\\\
-e VEGA_REFINITIV_WCO_API_KEY=<Refinitiv World Check One API Key> \\\\
-e VEGA_REFINITIV_WCO_API_SECRET=<Refinitiv World Check One API Secret> \\\\
-e VEGA_REFINITIV_WCO_GROUP_ID=<Refinitiv World Check One Group ID> \\\\
verifyvasp/enclave:v1.5.7
```

<br />

## Enclave Server Activation

### Activate Enclave Server and Check

* How to check if the enclave server is successfully activated
  * On the docker log, a message like “Listening port at 21117,” which means that it is listening through the port number previously set up, must be shown.
  * (However, an error log must not follow.)

<br />

### Enclave Server Health Check

* GET request via \<enclave endpoint>/healthcheck.
* In the VV central server, the health of each VASP enclave server is regularly (every 5-10 sec) checked.

<br />

### Enclave Server Reactivation

* If the Enclave docker stops, it should be reactivated.
* Or if the logs as follows are shown in the Enclave docker log, it should be reactivated.
  * “Unhandled Rejection detect: \<error message>”
  * “Uncaught Exception detect: \<error message>”