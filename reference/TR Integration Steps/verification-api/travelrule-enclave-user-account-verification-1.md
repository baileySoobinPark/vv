---
title: Request User Account Verification API
excerpt: >
  The Originating VASP can initiate verification flow by validating the
  beneficiary account by calling this Enclave API. The verification ensures that
  the specified wallet address belongs to the Beneficiary VASP.

  ### Verification Result Description

  The verification result is provided in the accountVerificationResult field of
  the response. If the result is `DENIED`, the reason field specifies the reason
  for the rejection.


  | Reason Code         | Description |

  |---------------------|-------------|

  | `UNKNOWN-SYMBOL`    | Error code returned when the symbol provided by the
  Originating VASP corresponds to an asset not supported by the Beneficiary
  VASP. |

  | `UNKNOWN-NETWORK`   | The error code returned when the network provided by
  the Originating VASP is either not supported by the Beneficiary VASP or the
  network information is insufficient.<br><br>This error is also returned if the
  asset symbol provided by the Originating VASP matches a symbol supported by
  the Beneficiary VASP but corresponds to a different network.<br><br>Example:
  If the Originating VASP sends a symbol field with USDT and a network field
  with `Ethereum` to the Beneficiary VASP, but the Beneficiary VASP only
  supports USDT on the Tron network. |

  | `UNKNOWN-ADDRESS`   | The error code returned when the Beneficiary's wallet
  address provided by the Originating VASP does not match any wallet address
  owned by the Beneficiary VASP or does not exist. |

  | `MISMATCHED-NAME`   | The error code returned when the Beneficiary's name
  provided by the Originating VASP does not match the name registered with the
  wallet address owned by the Beneficiary VASP or does not exist. |

  | `UNDEFINED-ERROR`   | The error code returned when an undefined error occurs
  outside of the four specified cases. |

  | `UNVERIFIED-KYC`    | Error code returned when the Beneficiary has not
  completed the KYC verification process with the Beneficiary VASP. |
api:
  file: TR_Enclave_API_Spec.json
  operationId: travelrule-Enclave-User-Account-Verification
hidden: false
---