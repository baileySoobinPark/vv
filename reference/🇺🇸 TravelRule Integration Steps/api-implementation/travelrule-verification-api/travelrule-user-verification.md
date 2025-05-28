---
title: Verify User API
excerpt: >
  This API must be implemented by your VASP to fulfill its role as a Beneficiary
  VASP. Its primary purpose is to verify the consistency of the originator user
  provided Beneficiary's personal information against the Beneficiary VASP
  managing data, while ensuring compliance with KYC/AML and sanction screening
  requirements.

  By providing this endpoint, your VASP allows the Enclave to delegate
  verification tasks. The Enclave will invoke this API upon receiving
  verification requests from the Originating VASP.


  ### Functional Requirements

  **1. Personal Information Verification**

    - The API must validate whether the personal information provided by the Originating VASP matches the data managed by the Beneficiary VASP.
    - Fields to be matched include:
        - Name: Verify that the Beneficiary's name matches the existing records.
        - Address: Verify that the account number or wallet address matches the existing records.


  **2. Compliance Assurance**

    The verification process must ensure regulatory compliance by performing the following checks and validations for the Beneficiary:
    - **KYC Validation**: Check if the Beneficiary has completed Know Your Customer (KYC) authentication.
    - **AML Validation**: Confirm the Beneficiary complies with Anti-Money Laundering (AML) requirements.
    - **STR Monitoring and Sanction Screening**: Perform Suspicious Transaction Reporting (STR) analysis and sanction screening for the Originator.
    - **Additional Filtering**: Your VASP may conduct extra filtering or verification of the provided personal information for both the Originator and Beneficiary


  **3. Responding to Originating VASP Data Requests**

    Your VASP (Beneficiary VASP) must provide all information requested by the Originating VASP in the requiredBeneficiaryInfo object during the verification process, by filling and returning the ivms101 object with the data.
    - If your VASP does not have or cannot provide the requested information, the API must set verificationResult to `DENIED` and reason to `UNAVAILABLE-INFORMATION`.
    - Only the information explicitly requested in requiredBeneficiaryInfo should be filled. Any fields not requested by the Originating VASP must remain empty.
    - The beneficiary account number (or wallet address) must be returned without modification. Altering the wallet address may result in incorrect deposits. If the deposit address is invalid or incorrect, the API must set verificationResult to `DENIED`.


  **4. Responding with Additional Beneficiary Information and Verification
  Results**

    The API must return the verification result.
    * After verifying both the Originator and Beneficiary information (including addresses and personal details), the API must return the result field as `VERIFIED` if the provided data matches the beneficiary side records and there are no exceptional reasons to deny the asset transfer.
    * If the provided information is insufficient or does not match the details on the Beneficiary's side, or if the account address or its owner is considered high-risk for asset transfer, the API must return the result field as `DENIED`.
    
    Upon successful user verification, the API must return additional personal information about the Beneficiary in the IVMS101 messaging format.


  **5. Failure Reason Specification**

    When the result field is set to `DENIED`, the reason field in the response must specify the reason for the failure. The allowed values for this field are as follows:

  | Reason Code             | Message                            | Description |

  |-------------------------|-------------------------------------|-------------|

  | `UNKNOWN-SYMBOL`        | `"ETH"`                             | Error code
  returned when the virtual asset symbol entered by the Originating VASP is not
  supported by the Beneficiary VASP. |

  | `UNKNOWN-NETWORK`       | `"Ethereum"`                        | Error code
  returned when the network entered by the Originating VASP is either not
  supported by the Beneficiary VASP or contains insufficient network
  information. <br>This error is also returned if the asset symbol matches but
  the network does not (e.g., sending `USDT` with `ETH` network when only `USDT`
  on Tron is supported). |

  | `UNKNOWN-ADDRESS`       | `"0x... is not registered."`        | Error code
  returned when the wallet address entered by the Originating VASP does not
  belong to a wallet managed by the Beneficiary VASP. |

  | `LACK-OF-INFORMATION`   | `"ACCOUNT_NUMBER"`                  | Error code
  returned when the information about the Originator is insufficient for the
  Beneficiary VASP to perform verification. |

  | `UNAVAILABLE-INFORMATION` | `"ACCOUNT_NUMBER"`               | Error code
  returned when the Beneficiary VASP does not possess or cannot provide certain
  personal information requested by the Originating VASP. |

  | `BLACKLISTED`           | `"0x.. is listed on the blacklist."` | Error code
  returned when the sanction screening results for the Originator indicate a
  compliance issue. |

  | `UNVERIFIED-KYC`        | `"0x.. is unverified KYC"`          | Error code
  returned when the Beneficiary has not completed the KYC verification process
  with the Beneficiary VASP.|

  | `MISMATCHED-NAME`       | `"Name is not matched."`            | Error code
  returned when the Beneficiary's name does not match the name provided by the
  Originating VASP. |

  | `NOT-ALLOWED`           | `"This user is locked by internal policy."` |
  Error code returned when the Beneficiary VASP rejects the user verification
  for any reason. |

  | `UNDEFINED-ERROR`       | `"Undefined Error is occurred."`    | Error code
  returned when an error occurs that is not defined in the specified cases. |

  ### Constraints


  - This API **must respond within 5 seconds**.


  ### Recommendations


  Virtual asset transfers **below the threshold set by regulations** are **not
  subject to the Travel Rule**, and **beneficiary name verification is not
  mandatory**.

  - **We highly recommend using the TravelRule Protocol for all transactions**,
  regardless of differing thresholds across jurisdictions, to ensure secure and
  compliant transfers reduce operational resources.


  - If the `isExceedingThreshold` field in the Originating VASP's request is set
  to `false` the transfer **is not subject to the Travel Rule**, and **User
  verification is not mandatory** in such cases, the virtual asset **can be
  transferred without further verification**.


  ### Environment Variable Configuration


  Set the following environment variables as per the integration guide to
  connect the implemented API with the Enclave.  

  For a complete list of Enclave environment variables, [click
  here](ref:travelrule-enclave-setup).


  - `VEGA_VERIFICATION_API_PATH`  
    Implement this API at the desired path (`{VASP_DEFINED_PATH_VERIFY_USER}`) and set the path in the variable.

  - `VEGA_VERIFICATION_AUTHORIZATION_TOKEN`  
    Set this variable to the **API key provided during your VerifyVASP onboarding process**.
api:
  file: TR_VASP_API_Spec.yaml
  operationId: travelrule-User-Verification
hidden: false
---