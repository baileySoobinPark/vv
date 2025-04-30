---
title: Database Setup
deprecated: false
hidden: true
metadata:
  robots: index
---
### 📄 Database Tables for Verification and Risk Assessment

<Table align={["left","left","left"]}>
  <thead>
    <tr>
      <th style={{ textAlign: "left" }}>
        Table Name
      </th>

      <th style={{ textAlign: "left" }}>
        Description
      </th>

      <th style={{ textAlign: "left" }}>
        Backup & Recovery
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td style={{ textAlign: "left" }}>
        **verifications**
      </td>

      <td style={{ textAlign: "left" }}>
        * A table for storing verification status and history. When `POST /verifications` API is called, the request/response result is recorded in the verifications table.
        * The following columns are encrypted:
          * `ivms101_originator`
          * `ivms101_originating_vasp`
          * `ivms101_beneficiary`
          * `ivms101_beneficiary_vasp`
        * Consider that each record in this table will consume approximately **4–5 KB per verification**.
      </td>

      <td style={{ textAlign: "left" }}>
        **Daily backup is recommended**, as the table stores verification history.
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        **chainalysis\_sanction\_results**
      </td>

      <td style={{ textAlign: "left" }}>
        * Optional
        * Stores risk assessment history from Chainalysis Sanction API calls.
        * Required only for VASPs using this API.
        * Each record consumes approximately **1–2 KB**.
      </td>

      <td style={{ textAlign: "left" }}>
        Regular backup is recommended.
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        **chainalysis\_kyt\_results**
      </td>

      <td style={{ textAlign: "left" }}>
        * Optional
        * A table for storing risk assessment history via Chainalysis KYT API call.
        * This table is required only for VASPs utilizing the Chainalysis KYT API.
        * Consider that each record in this table will consume approximately **2-3 KB per request**.
      </td>

      <td style={{ textAlign: "left" }}>
        Regular backup is recommended.
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        **chainalysis\_kyt\_alerts**
      </td>

      <td style={{ textAlign: "left" }}>
        * Optional
        * A table for storing risk assessment history via Chainalysis KYT API call.
        * This table is required only for VASPs utilizing the Chainalysis KYT API.
        * 1:n correspondence with the record in `chainalysis_kyt_results` table.
        * Consider that each record in this table will consume approximately **0-3 KB per request**.
      </td>

      <td style={{ textAlign: "left" }}>
        Regular backup is recommended.
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        **refinitiv\_wco\_results**
      </td>

      <td style={{ textAlign: "left" }}>
        * Optional
        * A table for storing risk assessment history related to the `Refinitiv WCO API`.
        * This table is required only for VASPs utilizing the `Refinitiv WCO API`.
        * Consider that each record in this table will consume approximately **2-3 KB per request**.
      </td>

      <td style={{ textAlign: "left" }}>
        Regular backup is recommended.
      </td>
    </tr>
  </tbody>
</Table>

<br />

## Table Creation Query

Enclave mode 를 TR 로 설정한 경우 데이터 베이스 유형에 적합한 테이블 생성 및 키 구성 쿼리를 실행하여 데이터 베이스 설정이 필요합니다.