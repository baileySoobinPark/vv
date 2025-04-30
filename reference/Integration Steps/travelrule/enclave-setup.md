---
title: Enclave Setup
deprecated: false
hidden: true
metadata:
  robots: index
---
## Environment Variables

| Environment Name                         | Default | Description                                                                  |
| ---------------------------------------- | ------- | ---------------------------------------------------------------------------- |
| `VEGA_VERIFICATION_API_PATH`             | –       | User Verification API (VASP API) Endpoint URL.                               |
| `VEGA_VERIFICATION_ACCOUNT_API_PATH`     | –       | User Account Verification API (VASP API) Endpoint URL.                       |
| `VEGA_VERIFICATION_TRANSACTION_API_PATH` | –       | Transaction State Query API (VASP API) Endpoint URL.                         |
| `VEGA_CHAINALYSIS_SANCTION_API_KEY`      | –       | Chainalysis Sanction API Key for risk assessment.                            |
| `VEGA_CHAINALYSIS_KYT_API_KEY`           | –       | Chainalysis KYT API Key for risk assessment.                                 |
| `VEGA_REFINITIV_WCO_API_KEY`             | –       | Refinitiv World Check One API Key for risk assessment.                       |
| `VEGA_REFINITIV_WCO_API_SECRET`          | –       | Refinitiv World-Check One Secret Key.                                        |
| `VEGA_REFINITIV_WCO_GROUP_ID`            | –       | Group ID in Refinitiv Console. Required for WCO API screening case grouping. |

<br />

## **Enclave API Integration**

링크 변경 필

Now, it's time to integrate the Travel Rule into your VASP's withdrawal process. Refer to the [**Best Practice Sequence Diagram**](https://kr-verifyvasp.gitbook.io/verifyvasp/4SIXfxCm5FxXbkiB9pkY/our-solutions/travelrule/flow-diagram) to identify the appropriate Enclave API calls at each step of the withdrawal process. For detailed API specifications, refer to following Enclave API References. Once the integration of Enclave API calls is complete, proceed to the testing phase to ensure seamless communication between both endpoints.

* [VASP API](https://kr-verifyvasp.gitbook.io/verifyvasp/4SIXfxCm5FxXbkiB9pkY/integration-steps/travelrule/enclave-installation/vasp-management-api)
* [Verification API](https://kr-verifyvasp.gitbook.io/verifyvasp/4SIXfxCm5FxXbkiB9pkY/integration-steps/travelrule/enclave-installation/verification-api)
* [Transaction API](https://kr-verifyvasp.gitbook.io/verifyvasp/4SIXfxCm5FxXbkiB9pkY/integration-steps/travelrule/enclave-installation/transaction-api)
* [Report API](https://kr-verifyvasp.gitbook.io/verifyvasp/4SIXfxCm5FxXbkiB9pkY/integration-steps/travelrule/enclave-installation/report-api)
* [Screening API](https://kr-verifyvasp.gitbook.io/verifyvasp/4SIXfxCm5FxXbkiB9pkY/integration-steps/travelrule/enclave-installation/screening-api)