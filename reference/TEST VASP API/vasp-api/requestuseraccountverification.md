---
title: Verify User Account API
excerpt: >
  This API must be implemented by your VASP to fulfill its role as a
  **Beneficiary VASP**.  

  Its primary purpose is to verify whether the beneficiary account
  address—designated by the Originator as the transfer destination—is owned by
  your VASP.


  By providing this endpoint, your VASP allows the **Enclave** to delegate
  verification tasks.  

  The Enclave will invoke this API upon receiving verification requests from the
  Originating VASP.


  ### Functional Requirements


  **1. Ownership Verification**  

  The API must validate whether the requested beneficiary account address exists
  in the VASP’s database.  

  Based on this verification, it should return the appropriate result.


  **2. Responding with Verification Results**  

  The API must return the verification result.  

  - If the account is owned by the VASP, the API must set the
  `accountVerificationResult` field value to `VERIFIED`.  

  - If the account is not recognized by the VASP, the API must set the
  `accountVerificationResult` field value to `DENIED`.


  **3. Failure Reason Specification**  

  When the `accountVerificationResult` field is set to `DENIED`, the `reason`
  field in the response must specify the reason for the failure.  

  The allowed values for this field are as follows:


  | Reason Code         | Description |

  |---------------------|-------------|

  | `UNKNOWN-SYMBOL`    | Error code returned when the symbol provided by the
  Originating VASP is not supported by the Beneficiary VASP. <br><br>**Case
  Example:** The virtual asset is not handled by the exchange. |

  | `UNKNOWN-NETWORK`   | Error code returned when the network provided by the
  Originating VASP is not supported by the Beneficiary VASP or lacks sufficient
  network information. <br><br>**Case Example:** If the requested asset's symbol
  is `USDT` and the network is `Ethereum`, but the Beneficiary VASP only
  supports USDT on the Tron network, this reason should be used. |

  | `UNKNOWN-ADDRESS`   | Error code returned when the provided wallet address
  does not correspond to a wallet managed by the Beneficiary VASP. |

  | `MISMATCHED-NAME`   | Error code returned when the Beneficiary's name does
  not match the name provided by the Originating VASP. |

  | `UNVERIFIED-KYC`    | Error code returned when the Beneficiary has not
  completed the KYC verification process required by the Beneficiary VASP. |

  | `UNDEFINED-ERROR`   | Error code returned when an undefined or unexpected
  error occurs. |


  ### Constraints
    * This API must respond within 1 second.

  ### Recommendations

  It is recommended that this API focus solely on verifying ownership and does
  not perform risk assessments such as KYC or Sanction Screening during the
  verification process.


  ### Environment Variable Configuration

  Set the following environment variables as per the guide to integrate the
  implemented API with the Enclave. For a complete list of Enclave environment
  variables, [click here]().
    * `VEGA_VERIFICATION_ACCOUNT_API_PATH`: Implement this API at the desired path({VASP_DEFINED_PATH_VERIFY_USER_ACCOUNT}) and set this variable accordingly.
    * `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`: Use the API key provided during your VerifyVASP onboarding process to configure this variable.
api:
  file: 2025_05_02_VASP_API_Spec.yaml
  operationId: requestUserAccountVerification
hidden: false
---