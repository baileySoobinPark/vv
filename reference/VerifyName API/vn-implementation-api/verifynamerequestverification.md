---
title: Request Verification API
excerpt: >
  This API must be implemented by your VASP to perform the owner verification.
  Its primary purpose is to verify whether the beneficiary and the originator
  are the same person. 

  If your VASP receives an owner verification request, it must return the
  verification results for the ticker, network, address, tag, tx_hash, and dti.
  If there is a match, the VASP must also return the corresponding beneficiary
  or originator information along with the verification results.


  ## Environment Variable Configuration
    Set the following environment variables as per the guide to integrate the implemented API with the Enclave. For a complete list of Enclave environment variables, click [here.]()
    - `VEGA_VERIFICATION_VERIFY_NAME_V2_API_PATH` : Implement this API at the desired path({VASP_DEFINED_PATH_VERIFY_NAME}) and set this variable accordingly.
    - `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: Use the API key provided during your VerifyVASP onboarding process to configure this variable.

  ## Functional Requirements
    **1. Request Verification**
    Requests are categorized into two types based on the value of the type field.
    - Post Verification
      - It means the reqeust the type is set to VerifyOriginator.
      - Use the tx_hash field to verify whether the transaction was initiated by your VASP.
      - Use the address field to confirm the beneficiary address of the transaction.
    - Pre Verification
      - It means the reqeust the type is set to VerifyOriginator.
      - Use the address field to verify whether it matches an address registered with your VASP.

    This API must return with the verification results for the values contains in the request body.
    The following describes the standard verification methods and responses based on the contents of the request body.

    | Parameter Name | Required | Description |
    |----------------|----------|-----------------|
    | `ticker`       | required | If the ticker is supported by your VASP, return `MATCHED`; otherwise, return `MISMATCHED`. |
    | `network`      | optional | If the network is supported by your VASP, return `MATCHED`; otherwise, return `MISMATCHED` or if there is no `network` field in the request body or your VASP does not have any information about the network, return `SKIPPED`. |
    | `address`      | required | If the value of the type field in the request body is `VerifyBeneficiary`, the system verifies whether the provided address matches a registered beneficiary address in your VASP. If the type is `VerifyOriginator`, the system verifies whether the address corresponds to the beneficiary address associated with the transaction. If a matching beneficiary address is found, return `MATCHED`; otherwise, return `MISMATCHED`. |
    | `tag`          | optional | If the address field is present in the request body, verify whether the address and tag match the values registered in your VASP. If the tag matches the registered value, return MATCHED; if it does not match, return MISMATCHED. If the address field is not provided in the request body or if the comparison cannot be performed, return SKIPPED. |
    | `tx_hash`      | If the tx_hash field and its value are present in the request body, compare it against the transaction hash values sent by your VASP. If a match is found, return MATCHED; if there is no match, return MISMATCHED. If the tx_hash field is not present or if the comparison cannot be performed, return SKIPPED. |
    | `dti`          | optional | If the dti field and its value are present in the request body, verify whether it corresponds to a Digital Token Identifier supported by your VASP. If it matches, return MATCHED; if it does not match, return MISMATCHED. If the dti field is not present or if the comparison cannot be performed, return SKIPPED. |

    **2. Return Verification Results**
      This API must return verification_results. If either the address field or the tx_hash field is MATCHED, your VASP must also return the corresponding owner information.
      If the reuqests is `Post Verification` and the address or the tx_hash field is MATCHED, your VASP must return the owner information using `debtor` field.
      If the reuqests is `Pre Verification` and the address or the tx_hash field is MATCHED, your VASP must return the owner information using `creditor` field.

      Example responses are provided below, depending on the request type and the type of owner information to be returned.
    <details>
      <summary>1. Post Verification and the Owner Information is `naturalPerson`.</summary>
      ``` json
        {
          "verification_results": {
            "ticker": "MATCHED",        
            "network": "MISMATCHED",    
            "address": "SKIPPED",       
            "tag": "SKIPPED",
            "tx_hash": "SKIPPED",      
            "dti": "SKIPPED",         
          },
          "debtor"?: {
            "name": "HONG KIL DONG",
            "supplementary_data"?: {
              "envelope": {
                "name"?: {                  
                  "first_name": "GIL DONG",
                  "last_name"?: "HONG",
                },
              }
            },
            "identification": {
              "private_identification"?: {
                "date_and_place_of_birth": {
                  "birth_date": "2025-01-01",
                }
              }
            }
          }
        }
      ```
    </details>
    <details>
      <summary>2. Post Verification and the Owner Information is `legalPerson`.</summary>
      ``` json
        {
          "verification_results": {
            "ticker": "MATCHED",        
            "network": "MISMATCHED",    
            "address": "SKIPPED",       
            "tag": "SKIPPED",
            "tx_hash": "SKIPPED",      
            "dti": "SKIPPED",         
          },
          "debtor"?: {
            "name": "HONG KIL DONG",
            "supplementary_data"?: {
              "envelope": {
                "name"?: {                  
                  "first_name": "GIL DONG",
                  "last_name"?: "HONG",
                },
              }
            },
            "identification": {
              "organisation_identification"?: {
                "supplementary_data": {
                  "envelope": {
                    "date_of_incorporation"?: "2020-01-01",
                  }
                },
              },
              "lei"?: "506700GE1G29325QX363",
              "bic"?: "KRKRKR"
              "other"?: {
                "identification": "5493001KJTIIGC8Y1R12",
                "issuer": "ISO17442",
              },
            }
          }
        }
      ```
    </details>
    <details>
      <summary>3. Pre Verification and the Owner Information is `naturalPerson`.</summary>
      ``` json
        {
          "verification_results": {
            "ticker": "MATCHED",        
            "network": "MISMATCHED",    
            "address": "SKIPPED",       
            "tag": "SKIPPED",
            "tx_hash": "SKIPPED",      
            "dti": "SKIPPED",         
          },
          "creditor"?: {
            "name": "HONG KIL DONG",
            "supplementary_data"?: {
              "envelope": {
                "name"?: {                  
                  "first_name": "GIL DONG",
                  "last_name"?: "HONG",
                },
              }
            },
            "identification": {
              "private_identification"?: {
                "date_and_place_of_birth": {
                  "birth_date": "2025-01-01",
                }
              }
            }
          }
        }
      ```
    </details>
    <details>
      <summary>4. Pre Verification and the Owner Information is `legalPerson`.</summary>
      ``` json
        {
          "verification_results": {
            "ticker": "MATCHED",        
            "network": "MISMATCHED",    
            "address": "SKIPPED",       
            "tag": "SKIPPED",
            "tx_hash": "SKIPPED",      
            "dti": "SKIPPED",         
          },
          "creditor"?: {
            "name": "HONG KIL DONG",
            "supplementary_data"?: {
              "envelope": {
                "name"?: {                  
                  "first_name": "GIL DONG",
                  "last_name"?: "HONG",
                },
              }
            },
            "identification": {
              "organisation_identification"?: {
                "supplementary_data": {
                  "envelope": {
                    "date_of_incorporation"?: "2020-01-01",
                  }
                },
              },
              "lei"?: "506700GE1G29325QX363",
              "bic"?: "KRKRKR"
              "other"?: {
                "identification": "5493001KJTIIGC8Y1R12",
                "issuer": "ISO17442",
              },
            }
          }
        }
      ```
    </details>
api:
  file: 2025_05_09_VN_VASP_API_Spec.yaml
  operationId: verifyNameRequestVerification
hidden: false
---