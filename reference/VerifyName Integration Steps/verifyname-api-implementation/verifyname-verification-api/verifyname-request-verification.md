---
title: VerifyName API
excerpt: >
  This API must be implemented by your VASP to **perform the owner
  verification**. 

  Its primary purpose is to verify whether the Beneficiary and the Originator
  are the same individual.

  When your VASP receives an owner verification request, it must check whether
  any of the values included in the request body match the information held by
  your VASP. If any matching values are found, your VASP must return the
  corresponding Beneficiary or Originator information as part of the
  verification result.


  ## Environment Variable Configuration
    To implement this API, you must configure the environment variables to integrate with the Enclave server. Please configure the following environment variables accordingly.
    - `VEGA_VERIFICATION_VERIFY_NAME_V2_API_PATH` : Implement this API at the desired path({VASP_DEFINED_PATH_VERIFY_NAME}) and set this variable accordingly.
    - `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: Use the API key provided during your VerifyVASP onboarding process to configure this variable.

    For a complete list of Enclave environment variables, [click here.](ref:verifyname-enclave-setup#/environment-variables)

  ## Functional Requirements
    **1. Request Verification**
    Requests can be classified into two types, and the implementation requirements differ depending on the type.
    - **Post Verification**
      - It means the request the type is set to VerifyOriginator.
      - Your VASP must verify whether the transaction specified by the tx_hash in the request was executed by your VASP.
      - Your VASP must verify whether the beneficiary address in the transaction executed by your VASP matches the value provided in the address field of the request.
    - **Pre Verification**
      - It means the request the type is set to VerifyBeneficiary.
      - Your VASP must verify whether the address provided in the request matches an address registered with your VASP.

    The table below describes the parameters used for verification. Even if some fields are optional in the request, the response must include verification results for all fields.
    Depending on the verification result, the value will be returned as `MATCHED`, `MISMATCHED`, or `SKIPPED`.

    | Parameter Name | Required | How to verify |
    |----------------|----------|-----------------|
    | `ticker`       | required | If your VASP supports the ticker, return `MATCHED`; otherwise, return `MISMATCHED`. |
    | `network`      | optional | If your VASP supports the network, return `MATCHED`; otherwise, return `MISMATCHED`. If this field is not provided or your VASP has no information about the network, return `SKIPPED`. |
    | `address`      | required | If the request is for pre-verification, your VASP must verify whether the provided address matches a beneficiary address registered with your VASP. <br/><br/> If the request is for post-verification, your VASP must verify whether the provided address matches the beneficiary address specified in the transaction. If the addresses match, return MATCHED; if they do not match, return MISMATCHED. |
    | `tag`          | optional | Your VASP must verify whether the provided tag matches a tag registered with your VASP. If it matches, return MATCHED; if it does not match, return MISMATCHED. If the tag is not provided in the request or if the comparison cannot be performed, return SKIPPED. |
    | `tx_hash`      | optional | Your VASP must verify whether the provided transaction hash matches a transaction executed by your VASP. If it matches, return MATCHED; if it does not match, return MISMATCHED. If the tx_hash field is not provided in the request or if the comparison cannot be performed, return SKIPPED. |
    | `dti`          | optional | Your VASP must verify whether the provided value corresponds to a Digital Token Identifier supported by your VASP. If it matches, return MATCHED; if it does not match, return MISMATCHED. If the dti field is not provided in the request or if the comparison cannot be performed, return SKIPPED. |

    **2. Return Verification Results**
    This API must return verification_results. If either the address field or the tx_hash field is MATCHED, your VASP must also return the corresponding owner information.
    - If the reuqests is `Post Verification` and the address or the tx_hash field is MATCHED, your VASP must return the owner information in the `debtor` field.
    - If the reuqests is `Pre Verification` and the address or the tx_hash field is MATCHED, your VASP must return the owner information in the `creditor` field.

    **3. Response Examples**
    Example responses are provided below, depending on the request type and the type of owner information to be returned.

  <details>
    <summary>1. Post Verification and the type of Owner Information is `naturalPerson`.</summary>

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
    <summary>2. Post Verification and the type of Owner Information is `legalPerson`.</summary>

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
    <summary>3. Pre Verification and the type of Owner Information is `naturalPerson`.</summary>

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
    <summary>4. Pre Verification and the type of Owner Information is `legalPerson`.</summary>

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
  file: VN_VASP_API_Spec.yaml
  operationId: verifyName-Request-Verification
hidden: false
---