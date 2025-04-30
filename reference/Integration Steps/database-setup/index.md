---
title: Database Setup
excerpt: >-
  The next step is to initiate the configuration of the Enclave server. Prior to
  setting up the Enclave server, ensure that the required database is fully
  configured and ready for use. This guide details the system requirements,
  policies, and definitions for configuring the database on the Enclave server. 
  ###
deprecated: false
hidden: true
metadata:
  robots: index
---
## **Choose and Install a Database**

To interact with the VerifyVASP Enclave server, configure a supported database to store key data, such as transaction logs and verification results. Choose a DBMS that suits your infrastructure and operations; MySQL is recommended for its simplicity and performance.

**Supported Databases:**

* **MySQL`Recommended`**
* **PostgreSQL**
* **MSSQL**
* **Oracle DBMS**

<br />

## **Table Description**

After completing the database installation, you must create the necessary tables for the VerifyVASP Enclave server. The schema includes **four mandatory tables** and **four optional tables**, which are only needed if your implementation integrates with third-party screening services like Chainalysis or Refinitiv. Refer to the table below for detailed descriptions of each table.

> ⚠️ **Considerations—storage size and recovery policy**
>
> Review the estimated record size included in the descriptions to ensure adequate storage allocation for anticipated request volumes. Additionally, follow the backup and recovery policies to maintain data integrity and enable reliable operations over time.

<Table align={["left","left","left"]}>
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
        **own\_keys**
      </td>

      <td>
        * A table for storing the public key of your VASP. The private key is saved as encrypted.
        * When the other VASP requests a public key inquiry, the requested type of public key can be returned by being fetched from this table.
        * The table stores the public key and private key of the Own VASP – the enclave VASP itself – both of which are used in personal information encryption during the verification process.
        * Consider that each record in this table will consume approximately **1 KB per key**.
      </td>

      <td>
        Regular backup is recommended.
      </td>
    </tr>

    <tr>
      <td>
        **counter\_party\_keys**
      </td>

      <td>
        * A table for storing the public key of the counterparty VASP. When `POST /verifications` API is called, it is checked whether the public key of the beneficiary VASP is being cached in this table. If not, an automatic request to the beneficiary VASP is sent and saved here.
        * According to the key type determined by the originating VASP, the public key of the beneficiary VASP is recorded. \<br> - This table stores the public key of the beneficiary VASP, which is used to encrypt personal information in the verification process.
        * Consider that each record in this table will consume approximately **1 KB per beneficiary address**.
      </td>

      <td>
        No backup or restoration is necessary; this table is used exclusively for caching keys.
      </td>
    </tr>

    <tr>
      <td>
        **commands**
      </td>

      <td>
        * A table for storing intermediate requests for asynchronously called APIs. This table is only used inside the enclave, not by VASP.
        * The intermediate requests in processing asynchronous APIs are stored here.
        * Consider that each record in this table will consume approximately **1–5 KB per asynchronous request**.
      </td>

      <td>
        No backup or restoration is required; this table contains only temporary intermediate processing data.
      </td>
    </tr>
  </tbody>
</Table>