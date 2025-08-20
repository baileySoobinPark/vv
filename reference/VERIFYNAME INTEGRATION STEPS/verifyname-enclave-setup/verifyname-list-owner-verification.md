---
title: List Owner Verification Result API
api:
  file: VN_Enclave_API_Spec.yaml
  operationId: verifyname-list-owner-verification
hidden: false
---
Retrieves the history of VerifyName verification results. Supports multiple query parameters (filters) such as verification status, date range, asset type, and counterparty VASP. Only items matching all specified conditions are returned in the response. All conditions are optional, and multiple conditions are combined with an **AND** operator.

***

## API Specification