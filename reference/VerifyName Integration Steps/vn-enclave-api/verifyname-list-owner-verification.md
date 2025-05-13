---
title: List Owner Verification Result API
excerpt: >
  This API retrieves the history of all previously requested owner
  verifications. 


  ### Query with Filters

  - This API supports a variety of query parameters, enabling you to filter and
  retrieve owner verification records that match specific criteria.

  - Filters are particularly useful for managing verification requests based on
  their status, timestamp, asset type, or counterparty VASP.

  - Additionally, you can identify a specific verification record matched with
  an on-chain transaction using these filters.

  - All query parameters are optional, and any specified parameters are applied
  using an AND operation to filter the results.

  - For a detailed list of supported filters, refer to the Query Parameter
  section in the specification below.
api:
  file: 2025_05_12_VN_Enclave_Spec.yaml
  operationId: verifyname-list-owner-verification
hidden: false
---