---
title: Database Setup
deprecated: false
hidden: true
metadata:
  robots: index
---
### 📄 Database Tables for Verification and Risk Assessment

<Table>
  <thead>
    <tr>
      <th>
        Table Name
      </th>

      <th>
        Description
      </th>

      <th>
        Backup & Recovery
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        **verifications**
      </td>

      <td>
        * A table for storing verification status and history. When `POST /verifications` API is called, the request/response result is recorded in the verifications table.
        * The following columns are encrypted:
          * `ivms101_originator`
          * `ivms101_originating_vasp`
          * `ivms101_beneficiary`
          * `ivms101_beneficiary_vasp`
        * Consider that each record in this table will consume approximately **4–5 KB per verification**.
      </td>

      <td>
        **Daily backup is recommended**, as the table stores verification history.
      </td>
    </tr>

    <tr>
      <td>
        **chainalysis\_sanction\_results**
      </td>

      <td>
        * Optional
        * Stores risk assessment history from Chainalysis Sanction API calls.
        * Required only for VASPs using this API.
        * Each record consumes approximately **1–2 KB**.
      </td>

      <td>
        Regular backup is recommended.
      </td>
    </tr>

    <tr>
      <td>
        **chainalysis\_kyt\_results**
      </td>

      <td>
        * Optional
        * A table for storing risk assessment history via Chainalysis KYT API call.
        * This table is required only for VASPs utilizing the Chainalysis KYT API.
        * Consider that each record in this table will consume approximately **2-3 KB per request**.
      </td>

      <td>
        Regular backup is recommended.
      </td>
    </tr>

    <tr>
      <td>
        **chainalysis\_kyt\_alerts**
      </td>

      <td>
        * Optional
        * A table for storing risk assessment history via Chainalysis KYT API call.
        * This table is required only for VASPs utilizing the Chainalysis KYT API.
        * 1:n correspondence with the record in `chainalysis_kyt_results` table.
        * Consider that each record in this table will consume approximately **0-3 KB per request**.
      </td>

      <td>
        Regular backup is recommended.
      </td>
    </tr>

    <tr>
      <td>
        **refinitiv\_wco\_results**
      </td>

      <td>
        * Optional
        * A table for storing risk assessment history related to the `Refinitiv WCO API`.
        * This table is required only for VASPs utilizing the `Refinitiv WCO API`.
        * Consider that each record in this table will consume approximately **2-3 KB per request**.
      </td>

      <td>
        Regular backup is recommended.
      </td>
    </tr>
  </tbody>
</Table>