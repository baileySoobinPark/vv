---
title: Callback API
api:
  file: TR_VASP_API_Spec.yaml
  operationId: travelrule-callback
hidden: false
---
The Callback API is a common interface used by both Ordering VASP and Beneficiary VASP roles in the TravelRule protocol. It is executed by the Enclave when a counterparty VASP calls a Report API.

***

## Implementation Guide

### Functional Requirements

#### 1. Callback Type Routing

* Branch your business logic based on the <code>callbackType</code> field in the request.
* Supported callback types:

<HTMLBlock>{`
<style>
  .custom-table {
    border-collapse: collapse;
    width: 100%;
    font-size: 14px;
  }

  .custom-table th,
  .custom-table td {
    border: 1px solid #ddd;
    padding: 12px;
    text-align: left;
    vertical-align: top;
  }

  .custom-table th {
    background-color: #f0f0f0;
    font-weight: 600;
  }

  .custom-table td {
    background-color: #ffffff;
  }

  .custom-table td.code-col {
    min-width: 200px;
    white-space: nowrap;
  }
</style>

<table class="custom-table">
  <thead>
    <tr>
      <th>CallbackType</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="code-col"><code>VERIFICATION_RESULT (Required)</code></td>
      <td>Used in the Ordering VASP role. Called when the beneficiary verification result is delivered asynchronously.</td>
    </tr>
    <tr>
      <td class="code-col"><code>TX_REPORT (Required)</code></td>
      <td>Used in the Beneficiary VASP role. Called when the Ordering VASP reports the transaction result.</td>
    </tr>
    <tr>
      <td class="code-col"><code>ERROR_REPORT (Required)</code></td>
      <td>Used in the Beneficiary VASP role. Called when the Ordering VASP reports an error.</td>
    </tr>
    <tr>
      <td class="code-col"><code>CHAINALYSIS_KYT_RESULT (Optional)</code></td>
      <td>Called when Chainalysis KYT results are delivered asynchronously.</td>
    </tr>
    <tr>
      <td class="code-col"><code>REFINITIV_WCO_RESULT (Optional)</code></td>
      <td>Called when Refinitiv WCO results are delivered asynchronously.</td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

<Callback />

<br />

#### 2. VERIFICATION_RESULT Handling

Perform follow-up actions based on the verification result:

* If **verified**, proceed to the next verification step or execute the transaction.
* If **denied**, stop the transfer and notify the customer. Use <code>data.reason</code> to include the failure reason in the message.

`VERIFICATION_RESULT` callback example.

<Accordion title="Example of Callback Request Body: VERIFIED" icon="fa-info-circle">
  ```json
  {
   "callbackType":"VERIFICATION_RESULT",
   "data": {
      "verificationUuid": "64ab871b-14a3-47df-9b80-368e29fe8180",
      "verificationResult": "VERIFIED",
      "reason": "OK",
      "message": "",
      "ivms101": {
         "beneficiary": {
            "beneficiaryPersons": [
               {
                  "naturalPerson": {
                     "name": {
                        "nameIdentifier": [
                           {
                              "primaryIdentifier": "James",
                              "nameIdentifierType": "LEGL"
                           }
                        ],
                        "localNameIdentifier": [
                           {
                              "primaryIdentifier": "김재원",
                              "nameIdentifierType": "LEGL"
                           }
                        ]
                     },
                     "geographicAddress": [
                        {
                           "addressType": "GEOG",
                           "townName": "Yeoksam-dong",
                           "addressLine": [
                              "14 Teheran-ro 4-gil, Gangnam-gu",
                              "4th floor"
                           ],
                           "country": "KR"
                        }
                     ],
                     "nationalIdentification": {
                        "nationalIdentifier": "12345-67890",
                        "nationalIdentifierType": "IDCD"
                     },
                     "customerIdentification": "1234569999",
                     "dateAndPlaceOfBirth": {
                        "dateOfBirth": "1985-03-14",
                        "placeOfBirth": "Nonsan"
                     },
                     "countryOfResidence": "KR"
                  }
               }
            ],
            "accountNumber": [
               "0xb0bFf9721871e22653358956cf59a5FdBF3D752F"
            ]
         }
      }
   }
  }
  ```
</Accordion>

<Accordion title="Example of Callback Request Body: DENIED" icon="fa-info-circle">
  ```json
  {
   "callbackType": "VERIFICATION_RESULT",
   "data": {
      "verificationUuid": "64ab871b-14a3-47df-9b80-368e29fe8180",
      "verificationResult": "DENIED",
      "reason": "UNKNOWN-ADDRESS",
      "ivms101": {
         "originator": {
            "originatorPersons": [
               {
                  "naturalPerson": {
                     "name": {
                        "nameIdentifier": [
                           {
                              "primaryIdentifier": "James",
                              "secondaryIdentifier": "Din",
                              "nameIdentifierType": "LEGL"
                           }
                        ]
                     }
                  }
               }
            ],
            "accountNumber": [
               "0x5811001506550d8356a215be229c15b6ef371a9a"
            ]
         },
         "beneficiary": {
            "beneficiaryPersons": [
               {
                  "naturalPerson": {
                     "name": {
                        "nameIdentifier": [
                           {
                              "primaryIdentifier": "Taylor",
                              "secondaryIdentifier": "Robbins",
                              "nameIdentifierType": "LEGL"
                           }
                        ]
                     }
                  }
               }
            ],
            "accountNumber": [
               "0xb0bFf9721871e22653358956cf59a5FdBF3D752F"
            ]
         },
         "originatingVASP": {
            "legalPerson": {
               "name": {
                  "nameIdentifier": [
                     {
                        "legalPersonName": "Lambda256",
                        "legalPersonNameIdentifierType": "LEGL"
                     }
                  ]
               },
               "geographicAddress": [
                  {
                     "addressType": "GEOG",
                     "postcode": "123123c",
                     "townName": "Yeoksam-dong",
                     "addressLine": [
                        "sechogu street",
                        "100-100"
                     ],
                     "country": "KR"
                  }
               ],
               "nationalIdentification": {
                  "nationalIdentifier": "KR0001",
                  "nationalIdentifierType": "RAID",
                  "registrationAuthority": "RA000151"
               }
            }
         },
         "beneficiaryVASP": {
            "legalPerson": {
               "name": {
                  "nameIdentifier": [
                     {
                        "legalPersonName": "Lambda256 Exchange",
                        "legalPersonNameIdentifierType": "LEGL"
                     }
                  ]
               },
               "geographicAddress": [
                  {
                     "addressType": "GEOG",
                     "postcode": "234234",
                     "townName": "Yeoksam-dong",
                     "addressLine": [
                        "sechogu street",
                        "100-100"
                     ],
                     "country": "KR"
                  }
               ],
               "nationalIdentification": {
                  "nationalIdentifier": "123456",
                  "nationalIdentifierType": "RAID",
                  "registrationAuthority": "RA000677"
               }
            }
         }
      }
   }
  }
  ```
</Accordion>

Possible failure reason codes when <code>data.result</code> is <code>DENIED</code> or <code>ERROR</code>:

<HTMLBlock>{`
<style>
  .custom-table {
    border-collapse: collapse;
    width: 100%;
    font-size: 14px;
  }

  .custom-table th,
  .custom-table td {
    border: 1px solid #ddd;
    padding: 10px;
    text-align: left;
    vertical-align: top;
  }

  .custom-table th {
    background-color: #f0f0f0;
    font-weight: 600;
  }

  .custom-table td {
    background-color: #ffffff;
  }

  .custom-table td.code-col {
    min-width: 150px;
    white-space: nowrap;
  }
</style>

<table class="custom-table">
  <thead>
    <tr>
      <th>reason</th>
      <th width=200px>message</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="code-col"><code>UNKNOWN-SYMBOL</code></td>
      <td>Unsupported asset symbol<br>(e.g., "ETH")</td>
      <td>A virtual asset not supported by the VASP (e.g., an asset not listed on the exchange)</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNKNOWN-NETWORK</code></td>
      <td>Unsupported network name<br>(e.g., "Ethereum")</td>
      <td>A network not supported by the VASP (e.g., USDT-Ethereum requested but only USDT-Tron supported)</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNKNOWN-ADDRESS</code></td>
      <td>Target address<br>(e.g., "0xasd...")</td>
      <td>Unrecognized wallet address</td>
    </tr>
    <tr>
      <td class="code-col"><code>LACK-OF-INFORMATION</code></td>
      <td>Missing fields separated by commas<br>(e.g.,"ACCOUNT_NUMBER")</td>
      <td>Insufficient originator information</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNAVAILABLE-INFORMATION</code></td>
      <td>Unavailable fields separated by commas<br>(e.g., "ACCOUNT_NUMBER")</td>
      <td>Beneficiary information unavailable</td>
    </tr>
    <tr>
      <td class="code-col"><code>BLACKLISTED</code></td>
      <td>Target address<br>(e.g., "0xasd...")</td>
      <td>Address is on a sanctions list</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNVERIFIED-KYC</code></td>
      <td>-</td>
      <td>KYC not completed</td>
    </tr>
    <tr>
      <td class="code-col"><code>MISMATCHED-NAME</code></td>
      <td>-</td>
      <td>Beneficiary name mismatch</td>
    </tr>
    <tr>
      <td class="code-col"><code>NOT-ALLOWED</code></td>
      <td>Reason<br>(e.g., "This user is locked by internal policy.")</td>
      <td>Blocked due to internal policy</td>
    </tr>
    <tr>
      <td class="code-col"><code>UNDEFINED-ERROR</code></td>
      <td>-</td>
      <td>Undefined or other error</td>
    </tr>
    <tr>
      <td class="code-col"><code>BENEFICIARY-ACCOUNT-NOT-MATCHED</code></td>
      <td>-</td>
      <td>Address returned by the Beneficiary VASP does not match the one sent by the Ordering VASP</td>
    </tr>
    <tr>
      <td class="code-col"><code>REQUEST-TIMEOUT</code></td>
      <td>-</td>
      <td>Verification request timed out</td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

<br />

#### 3. TX_REPORT Handling (Beneficiary VASP role)

* Verify that the reported transaction hash (txHash) corresponds to a transaction sent to the beneficiary’s actual deposit address.
* Record the result and verification history in your database.

<Accordion title="Example of Callback Request Body" icon="fa-info-circle">
  ```json
  {
  "callbackType": "TX_REPORT",
  "data": {
    "verificationUuid": "64ab871b-14a3-47df-9b80-368e29fe8181",
    "txHash": "8a54d58ca4100112a5430818776d74898f2232770bae03046862575cb851a042",
    "vout": "2"
  }
  }
  ```
</Accordion>

<br />

#### 4. ERROR_REPORT Handling (Beneficiary VASP role)

* Review the reported error details.
* Stop the transfer process, end any ongoing transaction tracking, and log the error.

<Accordion title="Example of Callback Request Body" icon="fa-info-circle">
  ```json
  {
   "callbackType": "ERROR_REPORT",
   "data": {
      "verificationUuid": "64ab871b-14a3-47df-9b80-368e29fe8182",
      "result": "ERROR",
      "reason": "BLACKLISTED",
      "message": "Originator is included in the blacklist."
   }
  }
  ```
</Accordion>

<br />

#### 5. CHAINALYSIS_KYT_RESULT Handling (Optional)

* Update sender or beneficiary risk assessment data based on the KYT results.
* Decide whether to allow or block the transaction.

<Accordion title="Example of Callback Request Body" icon="fa-info-circle">
  ```json
  {
   "callbackType": "CHAINALYSIS_KYT_RESULT",
   "data": {
      "verificationUuid": "69a310e6-810f-4a31-83d1-bcdafccf5304",
      "riskAssessment": {
         "chainalysisKYT": {
            "requestId": "f7231c6f-f1e7-4ae7-b143-2c87cd38abe9",
            "counterpartyVaspId": "15952089931162059995",
            "apiType": "ATTEMPT",
            "userId": "15952089931162059995",
            "direction": "OUTGOING",
            "network": "ETHEREUM",
            "asset": "ETH",
            "amount": "1",
            "usdPrice": "1820.17",
            "outputAddress": "bb3fd383d1c5540e52ef0a7bcb9433375793aeaf",
            "timestamp": "2023-05-18T12:39:44.000Z",
            "externalId": "79382ac9-c7be-3fab-ad56-8c61c654e2fc",
            "status": "PROCESSED",
            "alertCount": 1,
            "createdAt": "2023-05-18T12:39:46.000Z",
            "assessedAt": "2023-05-18T12:39:45.263Z"
         },
         "chainalysisKYTAlerts": [
            {
               "counterpartyVaspId": "15952089931162059995",
               "externalId": "79382ac9-c7be-3fab-ad56-8c61c654e2fc",
               "direction": "OUTGOING",
               "alertId": "118b8cc8-f579-11ed-b86d-a3210c6ca9b8",
               "alertLevel": "MEDIUM",
               "entityCategory": "high risk exchange",
               "serviceName": "HIGH RISK EXCHANGE: SimpleSwap.io bb3fd383d1c5540e52ef0a7bcb9433375793aeaf",
               "exposureType": "DIRECT",
               "alertAmount": "1820.17",
               "createdAt": "2023-05-18T12:39:52.461Z"
            }
         ]
      }
   }
  }
  ```
</Accordion>

<br />

#### 6. REFINITIV_WCO_RESULT Handling (Optional)

* Update sender or beneficiary risk assessment data based on the WCO results.
* Decide whether to allow, resume, or block the transaction.

<Accordion title="Example of Callback Request Body" icon="fa-info-circle">
  ```json
  {
  "callbackType": "REFINITIV_WCO_RESULT",
  "data": {
    "verificationUuid": "69a310e6-810f-4a31-83d1-bcdafccf5304",
    "riskAssessment": {
      "refinitivWorldCheckOne": {
        "counterpartyVaspId": "15952089931162058999",
        "direction": "INCOMING",
        "caseSystemId": "5jb7r2c9xjfk1hoc95gfayv6m",
        "status": "PROCESSED",
        "matchStrength": "EXACT",
        "aggregatedSummaryResult": "{\"caseId\":\"69a310e6-810f-4a31-83d1-bcdafccf5304-INCOMING-1684413585757\", ... }}}",
        "createdAt": "2023-05-18T12:39:48.000Z",
        "assessedAt": "2023-05-18T12:39:57.834Z"
      }
    }
  }
  }
  ```
</Accordion>

<br />

### Constraints

* Must respond **within 1 second**.
* The HTTP status code in the response must be <code>200 OK</code>.
* Idempotency is required – repeated delivery of the same callback must not change the outcome.
  * Example: Ignore duplicate requests if they have already been processed.

### Recommended Implementation

Response speed is critical.

* Perform heavy or time-consuming processing asynchronously after returning the API response.

### Enclave Integration Settings

Set the following Enclave environment variables for integration:

* `VEGA_VERIFICATION_CALLBACK_API_PATH`: API endpoint path
* `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: API authentication token value
* `VEGA_VERIFICATION_AUTHORIZATION_KEY`: HTTP header key for passing the authentication token

***

## API Specification