---
title: Get Owner Verification Result API
excerpt: >
  This API retrieves the result of a specific owner verification request using
  its unique request_id.


  The table below lists the valid values for the reason field and their
  corresponding descriptions.


  | Reason Code                              | Description |

  |-------------------------------------------|-------------|

  | MISMATCH-TICKER                           | Used with `verification_result:
  DENIED` when the owner verification result for the `ticker` field is
  `MISMATCHED` and the transaction is not sent. |

  | MISMATCH-NETWORK                          | Used with `verification_result:
  DENIED` when the owner verification result for the `network` field is
  `MISMATCHED` and the transaction is not sent. |

  | MISMATCH-ADDRESS                          | Used with `verification_result:
  DENIED` when the owner verification result for the `address` field is
  `MISMATCHED` and the transaction is not sent. |

  | MISMATCH-NAME                             | Used with `verification_result:
  DENIED` when the owner verification result for the `name` field is
  `MISMATCHED` and the transaction is not sent. |

  | MISMATCH-DTI                              | Used with `verification_result:
  DENIED` when the owner verification result for the `dti` field is `MISMATCHED`
  and the transaction is not sent. |

  | MISMATCH-DATE-OF-INCORPORATION            | Used with `verification_result:
  DENIED` when the owner verification result for the `date_of_incorporation`
  field is `MISMATCHED` and the transaction is not sent. |

  | MISMATCH-BIRTH-DATE                       | Used with `verification_result:
  DENIED` when the owner verification result for the `birth_date` field is
  `MISMATCHED` and the transaction is not sent. |

  | MISMATCH-ORGANISATION-IDENTIFICATION      | Used with `verification_result:
  DENIED` when the owner verification result for the
  `organisation_identification` field is `MISMATCHED` and the transaction is not
  sent. |

  | TRANSFER-ERROR                            | Used with `verification_result:
  ERROR` when a transaction transfer fails. |

  | UNDEFINED-ERROR                           | Used with `verification_result:
  ERROR` when an unexpected error occurs that is not covered by the above reason
  codes. |
api:
  file: 2025_05_12_VN_Enclave_Spec.yaml
  operationId: verifyname-get-owner-verification-result
hidden: false
---