---
title: Enclave Database Setup
excerpt: >-
  Once the VASP API implementation is complete, you can begin preparing to
  launch the Enclave server. Before starting the Enclave server, you must
  pre-configure the database that will be used by the Enclave so that it is
  ready for use. This guide describes in detail the system requirements,
  operational policies, and configuration parameters for setting up the database
  used by the Enclave server.
deprecated: false
hidden: false
metadata:
  robots: index
---
<Common_database />

## Step 1. Select and Install a DBMS

The VerifyVASP Enclave database stores key information such as verification results, transaction logs, and counterparty VASP public keys. Select a DBMS from the supported list below that best fits your infrastructure and operational environment.

### Supported DBMS

* **MySQL`Recommended`**
  * MySQL is recommended for its ease of installation/operation and excellent performance.
* **PostgreSQL**
* **MSSQL**
* **Oracle DBMS**

## Step 2. Create the Schema

Once the DBMS installation is complete, create the tables that will be used by the Enclave. The full schema consists of **four required tables**, three of which are shared with the Travel Rule module. A detailed description of each table is provided in the table below.

> ⚠️ Storage Size and Backup Policy Considerations
>
> Refer to the estimated record size for each table and ensure you have sufficient storage capacity to handle the expected request volume. To ensure **long-term data integrity** and **stable operations**, you must implement and apply appropriate backup and recovery policies.

<HTMLBlock>{`
<style>
  .custom-table {
    border-collapse: collapse;
    width: 100%;
    font-size: 14px;
  }

  .custom-table th,
  .custom-table td {
    border: 1px solid #ddd;
    padding: 12px;
    text-align: left;
    vertical-align: top;
  }

  .custom-table th {
    background-color: #f0f0f0;
    font-weight: 600;
  }

  .custom-table td {
    background-color: #ffffff;
  }

  .custom-table td.code-col {
    min-width: 180px;
    white-space: nowrap;
    font-family: monospace;
  }
</style>

<table class="custom-table">
  <thead>
    <tr>
      <th width=185px>Table</th>
      <th>Description</th>
      <th width=140px>Backup Policy</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="code-col"><code>own_keys</code></td>
      <td>
        - <b>Required table.</b> <br>
        - Stores the public/private key pair for your VASP Enclave.<br>
        - Returned when a counterparty VASP requests your public key.<br>
        - Encrypted field: <code>private_key.</code><br>
        - Estimated record size: <strong>1 KB</strong>.
      </td>
      <td>Periodic backup recommended</td>
    </tr>
    <tr>
      <td class="code-col"><code>counter_party_keys</code></td>
      <td>
        - <b>Required table.</b> <br>
        - Caches and stores public keys from counterparty VASPs.<br>
        - Estimated record size: <strong>1 KB</strong>.
      </td>
      <td>No backup or restore policy needed</td>
    </tr>
    <tr>
      <td class="code-col"><code>commands</code></td>
      <td>
        - <b>Required table.</b> <br>
        - Stores intermediate states for asynchronous API processing within the Enclave.<br> 
        - Estimated record size: <strong>1-5 KB</strong>.
      </td>
      <td>No backup or restore policy needed</td>
    </tr>
		<tr>
      <td class="code-col"><code>owner_verifications</code></td>
      <td>
        - <b>Required table.</b> <br>
        - Stores account ownership verification requests, results, and history.<br> 
 				- Contains request/response data from VerifyName 2.0 API calls.<br>
        - Encrypted fields: <code>party_info</code>,<code>party_info_hash</code><br>
        - Estimated record size: <strong>4-5 KB</strong>.
      </td>
      <td>Daily backup recommended</td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

### TravelRule / VerifyName Common Required Table Creation Queries

If your VASP implements both TravelRule and VerifyName protocols, the following queries create the **required tables** used by both protocols.\
If these tables were already created during the VerifyName integration process, you may skip this step.

<Tabs>
  <Tab title="MySQL">
    ```sql
    CREATE TABLE `own_keys` (
      `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT 'Key ID',
      `type` enum('PerVasp', 'PerAddress', 'PerVerification') NOT NULL COMMENT 'Key types',
      `key_identifier` varchar(256) NOT NULL COMMENT 'Identifier of key (address or type or public key)',
      `public_key` varchar(256) NOT NULL COMMENT 'Public Key',
      `private_key` varchar(256) NOT NULL COMMENT 'Private Key',
      `created_at` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT 'Created at.',
      PRIMARY KEY (`id`),
      UNIQUE KEY `key_uniqueness` (`key_identifier`, `type`),
      INDEX `public_key` (`public_key`, `private_key`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;

    CREATE TABLE `counter_party_keys` (
      `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT 'Key ID',
      `type` enum('PerVasp', 'PerAddress', 'PerVerification') NOT NULL COMMENT 'Key types',
      `vasp_id` bigint(20) unsigned NOT NULL COMMENT 'Counter party VASP ID',
      `key_identifier` varchar(256) NOT NULL COMMENT 'Identifier of key (address or vaspId)',
      `public_key` varchar(256) NOT NULL COMMENT 'Public Key of counter party',
      `created_at` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT 'Created at.',
      PRIMARY KEY (`id`),
      UNIQUE KEY `key_uniqueness` (`vasp_id`, `key_identifier`, `type`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;

    CREATE TABLE `commands` (
      `command_id` bigint(20) unsigned NOT NULL COMMENT 'Command ID',
      `command_type` varchar(32) NOT NULL COMMENT 'Command type',
      `command_body` text(65535) NOT NULL COMMENT 'Command body',
      `status` enum('CREATED', 'PROCESSING', 'DONE', 'ERROR') NOT NULL DEFAULT 'CREATED' COMMENT 'Command status',
      `created_at` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT 'Created at.',
      `fetched_at` datetime DEFAULT NULL COMMENT 'Fetched at.',
      `finished_at` datetime DEFAULT NULL COMMENT 'Finished at.',
      PRIMARY KEY (`command_id`),
      INDEX `status_id` (`status`, `command_id`),
      INDEX `status_finished_at` (`status`, `finished_at`),
      INDEX `status_fetched_at_created_at` (`status`, `fetched_at`, `created_at`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
    ```
  </Tab>

  <Tab title="PostgreSQL">
    ```sql
    CREATE TABLE own_keys (
      id SERIAL NOT NULL PRIMARY KEY,
      type enum_key_types NOT NULL,
      key_identifier varchar(256) NOT NULL,
      UNIQUE(key_identifier, type),
      public_key varchar(256) NOT NULL,
      private_key varchar(256) NOT NULL,
      created_at timestamp DEFAULT CURRENT_TIMESTAMP
    );

    CREATE INDEX public_key ON own_keys(public_key, private_key);

    CREATE TYPE enum_key_types AS ENUM ('PerVasp', 'PerAddress', 'PerVerification');

    CREATE TABLE counter_party_keys (
      id SERIAL NOT NULL PRIMARY KEY,
      type enum_key_types NOT NULL,
      vasp_id numeric(20) NOT NULL,
      key_identifier varchar(256) NOT NULL,
      UNIQUE(vasp_id, key_identifier, type),
      public_key varchar(256) NOT NULL,
      created_at timestamp DEFAULT CURRENT_TIMESTAMP
    );

    CREATE type enum_status as ENUM('CREATED', 'PROCESSING', 'DONE', 'ERROR');

    CREATE TABLE commands(
      command_id numeric(20) NOT NULL PRIMARY KEY,
      command_type varchar(32) NOT NULL,
      command_body text NOT NULL,
      status enum_status NOT NULL DEFAULT 'CREATED',
      created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
      fetched_at timestamp DEFAULT NULL,
      finished_at timestamp DEFAULT NULL
    );

    CREATE INDEX status_id ON commands(status, command_id);
    CREATE INDEX status_finished_at ON  commands(status, finished_at);
    CREATE INDEX status_fetched_at_created_at ON commands(status, fetched_at, created_at);
    ```
  </Tab>

  <Tab title="MSSQL">
    ```sql
    CREATE TABLE own_keys (
      id BIGINT IDENTITY(1,1) NOT NULL PRIMARY KEY,
      type nvarchar(20) NOT NULL check (type in ('PerVasp', 'PerAddress', 'PerVerification')),
      key_identifier nvarchar(256) NOT NULL,
      public_key nvarchar(256) NOT NULL,
      private_key nvarchar(256) NOT NULL,
      created_at datetime2 DEFAULT CURRENT_TIMESTAMP,
      CONSTRAINT key_uniq_own_keys UNIQUE(key_identifier, type)
    );

    CREATE INDEX public_key ON own_keys(public_key, private_key);

    CREATE TABLE counter_party_keys (
      id BIGINT IDENTITY(1,1) NOT NULL PRIMARY KEY,
      type nvarchar(20) NOT NULL check (type in ('PerVasp', 'PerAddress', 'PerVerification')),
      vasp_id BIGINT check (vasp_id > 0) NOT NULL,
      key_identifier nvarchar(256) NOT NULL,
      public_key nvarchar(256) NOT NULL,
      created_at datetime2 DEFAULT CURRENT_TIMESTAMP,
      CONSTRAINT key_uniq_counter_party_keys UNIQUE(vasp_id, key_identifier, type)
    );

    CREATE TABLE commands (
      command_id BIGINT NOT NULL PRIMARY KEY,
      command_type nvarchar(32) NOT NULL,
      command_body nvarchar(MAX) NOT NULL,
      status nvarchar(20) DEFAULT 'CREATED' NOT NULL check (status in ('CREATED', 'PROCESSING', 'DONE', 'ERROR')),
      created_at datetime2 DEFAULT CURRENT_TIMESTAMP,
      fetched_at datetime2 DEFAULT NULL,
      finished_at datetime2 DEFAULT NULL
    );

    CREATE INDEX idx_status_id ON commands(status, command_id);
    CREATE INDEX idx_status_finished_at ON commands(status, finished_at);
    CREATE INDEX idx_status_fetched_at_created_at ON commands(status, fetched_at, created_at);
    ```
  </Tab>

  <Tab title="Oracle">
    ```sql
    CREATE TABLE "own_keys" (
      "id" number(20) NOT NULL,
      "type" varchar2(20) NOT NULL check ("type" in ('PerVasp', 'PerAddress', 'PerVerification')),
      "key_identifier" varchar2(256) NOT NULL,
      "public_key" varchar2(256) NOT NULL,
      "private_key" varchar2(256) NOT NULL,
      "created_at" date DEFAULT CURRENT_TIMESTAMP,
      CONSTRAINT "pk_own_keys_id" PRIMARY KEY ("id"),
      CONSTRAINT "key_uniq_own_keys" UNIQUE ("key_identifier", "type")
    );

    CREATE INDEX "idx_public_key" ON "own_keys"("public_key", "private_key");
    CREATE SEQUENCE "own_keys_id_seq";

    CREATE OR REPLACE TRIGGER own_keys_trigger
    BEFORE INSERT ON "own_keys"
    FOR EACH ROW
    BEGIN
    SELECT "own_keys_id_seq".nextval
    INTO :new."id"
    FROM dual;
    END;

    CREATE TABLE "counter_party_keys" (
      "id" number(20) NOT NULL,
      "type" varchar2(20) NOT NULL check ("type" in ('PerVasp', 'PerAddress', 'PerVerification')),
      "vasp_id" varchar2(20) NOT NULL,
      "key_identifier" varchar2(256) NOT NULL,
      "public_key" varchar2(256) NOT NULL,
      "created_at" date DEFAULT CURRENT_TIMESTAMP,
      CONSTRAINT "pk_counter_party_keys_id" PRIMARY KEY ("id"),
      CONSTRAINT "key_uniq_counter_party_keys" UNIQUE ("vasp_id", "key_identifier", "type")
    );

    CREATE SEQUENCE "counter_party_keys_id_seq";

    CREATE OR REPLACE TRIGGER counter_party_keys_trigger
    BEFORE INSERT ON "counter_party_keys"
    FOR EACH ROW
    BEGIN
    SELECT "counter_party_keys_id_seq".nextval
    INTO :new."id"
    FROM dual;
    END;

    CREATE TABLE "commands" (
      "command_id" number(20) NOT NULL,
      "command_type" varchar2(32) NOT NULL,
      "command_body" clob NOT NULL,
      "status" varchar2(20) DEFAULT 'CREATED' NOT NULL check ("status" in ('CREATED', 'PROCESSING', 'DONE', 'ERROR')),
      "created_at" date DEFAULT CURRENT_TIMESTAMP,
      "fetched_at" date DEFAULT NULL,
      "finished_at" date DEFAULT NULL,
      CONSTRAINT "command_id" PRIMARY KEY ("command_id")
    );

    CREATE INDEX "idx_status_id" ON "commands"("status", "command_id");
    CREATE INDEX "idx_status_finished_at" ON "commands"("status", "finished_at");
    CREATE INDEX "idx_status_fetched_at_created_at" ON "commands"("status", "fetched_at", "created_at");
    ```
  </Tab>
</Tabs>

### VerifyName Required Table Creation Queries

The following queries create the tables required exclusively by the VerifyName protocol.\
If running the Enclave in VN2 mode, you must execute these queries to create the required tables and configure the associated keys.

<Tabs>
  <Tab title="MySQL">
    ```sql
    CREATE TABLE `owner_verifications` (
      `verification_id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
      `request_id` varchar(40) NOT NULL,
      `type` varchar(40) NOT NULL,
      `request_vasp_id` bigint(20) unsigned NOT NULL,
      `response_vasp_id` bigint(20) unsigned NOT NULL,
      `ticker` varchar(16) NOT NULL,
      `tx_hash` varchar(256) DEFAULT NULL,
      `network` varchar(256) DEFAULT NULL,
      `address` varchar(512) DEFAULT NULL,
      `tag` varchar(128) DEFAULT NULL,
      `dti` varchar(16) DEFAULT NULL COMMENT 'Degital Token Identifier',
      `vout` varchar(128) DEFAULT NULL,
      `salt` varchar(128) DEFAULT NULL COMMENT 'Party Info Hash Salt',
      `party_info` text(65535) DEFAULT NULL COMMENT 'Deptor or Creditor Information',
      `party_info_hash` text(65535) DEFAULT NULL COMMENT 'Deptor or Creditor Information Hash',
      `verification_results` varchar(4000) DEFAULT NULL,
      `verification_result` varchar(128) DEFAULT NULL COMMENT 'Final Verification Result',
      `status` varchar(40) NOT NULL,
      `reason` varchar(256) DEFAULT NULL,
      `message` varchar(1024) DEFAULT NULL,
      `version` varchar(16) DEFAULT NULL,
      `verified_at` datetime(3) DEFAULT NULL,
      `ordered_at` datetime(3) DEFAULT NULL,
      `result_reported_at` datetime(3) DEFAULT NULL,
      `updated_at` datetime(3) DEFAULT CURRENT_TIMESTAMP(3) ON UPDATE CURRENT_TIMESTAMP(3),
      `created_at` datetime(3) DEFAULT CURRENT_TIMESTAMP(3),
      PRIMARY KEY (`verification_id`),
      UNIQUE KEY `uk_owner_verifications_request_id` (`request_id`),
      KEY `idx_request_vasp_id_response_vasp_id` (`request_vasp_id`,`response_vasp_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
    ```
  </Tab>

  <Tab title="PostgreSQL">
    ```sql
    CREATE TYPE enum_owner_verifications_type AS ENUM('VerifyOriginator', 'VerifyBeneficiary');

    CREATE TABLE owner_verifications (
      verification_id SERIAL NOT NULL PRIMARY KEY,
      request_id varchar(40) NOT NULL,
      type enum_owner_verifications_type NOT NULL,
      request_vasp_id numeric(20) NOT NULL,
      response_vasp_id numeric(20) NOT NULL,
      ticker varchar(16) NOT NULL,
      tx_hash varchar(256) DEFAULT NULL,
      network varchar(256) DEFAULT NULL,
      address varchar(512) DEFAULT NULL,
      tag varchar(128) DEFAULT NULL,
      dti varchar(16) DEFAULT NULL,
      vout varchar(128) DEFAULT NULL,
      salt varchar(128) DEFAULT NULL,
      party_info text DEFAULT NULL,
      party_info_hash text DEFAULT NULL,
      verification_results varchar(4000) DEFAULT NULL,
      verification_result varchar(128) DEFAULT NULL,
      status varchar(40) NOT NULL,
      reason varchar(256) DEFAULT NULL,
      message varchar(1024) DEFAULT NULL,
      version varchar(16) DEFAULT NULL,
      verified_at timestamp DEFAULT NULL,
      ordered_at timestamp DEFAULT NULL,
      result_reported_at timestamp DEFAULT NULL,
      updated_at timestamp DEFAULT CURRENT_TIMESTAMP,
      created_at timestamp DEFAULT CURRENT_TIMESTAMP,
      CONSTRAINT key_uniq_owner_verifications_request_id UNIQUE (request_id)
    );

    CREATE INDEX idx_request_vasp_id_response_vasp_id ON owner_verifications(request_vasp_id, response_vasp_id);
    ```
  </Tab>

  <Tab title="MSSQL">
    ```sql
    CREATE TABLE owner_verifications (
      verification_id BIGINT IDENTITY(1,1) NOT NULL PRIMARY KEY,
      request_id nvarchar(40) NOT NULL UNIQUE,
      type nvarchar(40) NOT NULL check (type in ('VerifyOriginator', 'VerifyBeneficiary')),
      request_vasp_id BIGINT NOT NULL,
      response_vasp_id BIGINT NOT NULL,
      ticker nvarchar(16) NOT NULL,
      tx_hash nvarchar(256) DEFAULT NULL,
      network nvarchar(256) DEFAULT NULL,
      address nvarchar(512) DEFAULT NULL,
      tag nvarchar(128) DEFAULT NULL,
      dti nvarchar(16) DEFAULT NULL,
      vout nvarchar(128) DEFAULT NULL,
      salt nvarchar(128) DEFAULT NULL,
      party_info nvarchar(max) DEFAULT NULL,
      party_info_hash nvarchar(max) DEFAULT NULL,
      verification_results nvarchar(4000) DEFAULT NULL,
      verification_result nvarchar(128) DEFAULT NULL,
      status nvarchar(40) NOT NULL,
      reason nvarchar(256) DEFAULT NULL,
      message nvarchar(1024) DEFAULT NULL,
      version nvarchar(16) DEFAULT NULL,
      verified_at datetime2(3) DEFAULT NULL,
      ordered_at datetime2(3) DEFAULT NULL,
      result_reported_at datetime2(3) DEFAULT NULL,
      updated_at datetime2(3) DEFAULT CURRENT_TIMESTAMP,
      created_at datetime2(3) DEFAULT CURRENT_TIMESTAMP,
    );

    CREATE INDEX idx_request_vasp_id_response_vasp_id ON owner_verifications(request_vasp_id, response_vasp_id);
    ```
  </Tab>

  <Tab title="Oracle">
    ```sql
    CREATE TABLE "owner_verifications" (
      "verification_id" number(20) NOT NULL,
      "request_id" varchar2(40) NOT NULL,
      "type" varchar2(20) NOT NULL CHECK ("type" IN ('VerifyOriginator', 'VerifyBeneficiary')),
      "request_vasp_id" varchar2(20) NOT NULL,
      "response_vasp_id" varchar2(20) NOT NULL,
      "ticker" varchar2(16) NOT NULL,
      "tx_hash" varchar2(256) DEFAULT NULL,
      "network" varchar2(256) DEFAULT NULL,
      "address" varchar2(512) DEFAULT NULL,
      "tag" varchar2(128) DEFAULT NULL,
      "dti" varchar2(16) DEFAULT NULL,
      "vout" varchar2(128) DEFAULT NULL,
      "salt" varchar2(128) DEFAULT NULL,
      "party_info" clob DEFAULT NULL,
      "party_info_hash" clob DEFAULT NULL,
      "verification_results" varchar2(4000) DEFAULT NULL,
      "verification_result" varchar2(128) DEFAULT NULL,
      "status" varchar2(40) NOT NULL,
      "reason" varchar2(256) DEFAULT NULL,
      "message" varchar2(1024) DEFAULT NULL,
      "version" varchar2(16) DEFAULT NULL,
      "verified_at" timestamp(3) DEFAULT NULL,
      "ordered_at" timestamp(3) DEFAULT NULL,
      "result_reported_at" timestamp(3) DEFAULT NULL,
      "updated_at" timestamp(3) DEFAULT CURRENT_TIMESTAMP,
      "created_at" timestamp(3) DEFAULT CURRENT_TIMESTAMP,
      CONSTRAINT "owner_verifications_pk" PRIMARY KEY ("verification_id"),
      CONSTRAINT "uniq_owner_verifications_request_id" UNIQUE ("request_id")
    );

    CREATE INDEX "idx_owner_verifications_request_vasp_id_response_vasp_id" ON "owner_verifications" ("request_vasp_id", "response_vasp_id");

    -- Create a sequence
    CREATE SEQUENCE "owner_verifications_seq";

    -- Create a trigger
    CREATE OR REPLACE TRIGGER owner_verifications_trg
    BEFORE INSERT ON "owner_verifications"
    FOR EACH ROW
    BEGIN
    SELECT "owner_verifications_seq".NEXTVAL
    INTO :new."verification_id"
    FROM dual;
    END;
    ```
  </Tab>
</Tabs>