---
title: Database Setup
deprecated: false
hidden: true
metadata:
  robots: index
---
## 📄 Database Tables for Verification and Risk Assessment

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

<Tabs>
  <Tab title="MySQL">
    ```sql
    CREATE TABLE `verifications` (
    `verification_id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT 'Verification ID',
    `verification_uuid` varchar(40) NOT NULL COMMENT 'Verification UUID',
    `result` enum('WAIT', 'VERIFIED', 'DENIED', 'UNKNOWN', 'ERROR', 'PENDING', 'TRANSFER_ERROR') NOT NULL DEFAULT 'WAIT' COMMENT 'Verification Result',
    `reason` varchar(256) DEFAULT NULL COMMENT 'Reason',
    `message` varchar(1024) DEFAULT NULL COMMENT 'Additional information about the reason',
    `network` varchar(128) DEFAULT NULL COMMENT 'Network for token transfer',
    `symbol` varchar(16) DEFAULT NULL COMMENT 'Symbol',
    `amount` varchar(128) DEFAULT NULL COMMENT 'Amount',
    `trade_price` varchar(128) DEFAULT NULL COMMENT 'Trading Price',
    `trade_currency` varchar(128) DEFAULT NULL COMMENT 'Trading Currency',
    `trade_iso_datetime` datetime DEFAULT NULL COMMENT 'Trading time',
    `is_exceeding_threshold` tinyint(1) NOT NULL DEFAULT 1 COMMENT 'Whether the threshold has been exceeded',
    `tx_hash` varchar(128) DEFAULT NULL COMMENT 'Tx hash',
    `vout` varchar(128) DEFAULT NULL COMMENT 'Vout',
    `originating_vasp_id` bigint(20) unsigned DEFAULT NULL COMMENT 'Originating VASP ID',
    `originator_account_number` varchar(256) DEFAULT NULL COMMENT 'Originator Account Address or ID',
    `ivms101_originator` text(65535) DEFAULT NULL COMMENT 'Originator Information',
    `ivms101_originating_vasp` text(65535) DEFAULT NULL COMMENT 'Originating VASP Information',
    `beneficiary_vasp_id` bigint(20) unsigned DEFAULT NULL COMMENT 'Beneficiary Vasp ID',
    `beneficiary_account_number` varchar(256) DEFAULT NULL COMMENT 'Beneficiary Account Address or ID',
    `ivms101_beneficiary` text(65535) DEFAULT NULL COMMENT 'Beneficiary Information',
    `ivms101_beneficiary_vasp` text(65535) DEFAULT NULL COMMENT 'Beneficiary VASP Information',
    `verified_at` datetime(3) DEFAULT NULL COMMENT 'Verified at.',
    `ordered_at` datetime(3) DEFAULT NULL COMMENT 'Ordered at.',
    `created_at` datetime(3) NOT NULL DEFAULT CURRENT_TIMESTAMP(3) COMMENT 'Created at.',
    PRIMARY KEY (`verification_id`),
    UNIQUE KEY `uk_verifications_verification_uuid` (`verification_uuid`),
    INDEX `idx_verifications_originator_account` (`originator_account_number`),
    INDEX `idx_verifications_beneficiary_account` (`beneficiary_account_number`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
    ```
  </Tab>

  <Tab title="PostgreSQL">
    ```sql
    CREATE TYPE enum_result AS ENUM ('WAIT', 'VERIFIED', 'UNKNOWN', 'DENIED', 'ERROR', 'PENDING', 'TRANSFER_ERROR');

    CREATE TABLE verifications (
    verification_id SERIAL NOT NULL PRIMARY KEY,
    verification_uuid varchar(40) NOT NULL,
    UNIQUE(verification_uuid),
    result enum_result DEFAULT 'WAIT',
    reason varchar(256) DEFAULT NULL,
    message varchar(1024) DEFAULT NULL,
    network varchar(256) DEFAULT NULL,
    symbol varchar(16) DEFAULT NULL,
    amount varchar(128) DEFAULT NULL,
    trade_price varchar(128) DEFAULT NULL,
    trade_currency varchar(128) DEFAULT NULL,
    trade_iso_datetime timestamp DEFAULT NULL,
    is_exceeding_threshold boolean DEFAULT true NOT NULL,
    tx_hash varchar(128) DEFAULT NULL,
    vout varchar(128) DEFAULT NULL,
    originating_vasp_id numeric(20) DEFAULT NULL,
    originator_account_number varchar(256) DEFAULT NULL,
    ivms101_originator varchar(65535) DEFAULT NULL,
    ivms101_originating_vasp varchar(65535) DEFAULT NULL,
    beneficiary_vasp_id numeric(20) DEFAULT NULL,
    beneficiary_account_number varchar(256) DEFAULT NULL,
    ivms101_beneficiary varchar(65535) DEFAULT NULL,
    ivms101_beneficiary_vasp varchar(65535) DEFAULT NULL,
    verified_at timestamp DEFAULT NULL,
    ordered_at timestamp DEFAULT NULL,
    created_at timestamp DEFAULT CURRENT_TIMESTAMP
    );

    CREATE INDEX idx_verifications_originator_account ON verifications(originator_account_number);
    CREATE INDEX idx_verifications_beneficiary_account ON verifications(beneficiary_account_number);
    ```
  </Tab>

  <Tab title="MSSQL">
    ```sql
    CREATE TABLE verifications (
    verification_id BIGINT IDENTITY(1,1) NOT NULL PRIMARY KEY,
    verification_uuid nvarchar(40) NOT NULL UNIQUE,
    result nvarchar(20) DEFAULT 'WAIT' NOT NULL check (result in ('WAIT', 'VERIFIED', 'UNKNOWN', 'DENIED', 'ERROR', 'PENDING', 'TRANSFER_ERROR')),
    reason nvarchar(256) DEFAULT NULL,
    message nvarchar(1024) DEFAULT NULL,
    network nvarchar(128) DEFAULT NULL,
    symbol nvarchar(16) DEFAULT NULL,
    amount nvarchar(128) DEFAULT NULL,
    trade_price nvarchar(128) DEFAULT NULL,
    trade_currency nvarchar(128) DEFAULT NULL,
    trade_iso_datetime datetime2 DEFAULT NULL,
    is_exceeding_threshold tinyint DEFAULT 1 NOT NULL,
    tx_hash nvarchar(128) DEFAULT NULL,
    vout nvarchar(128) DEFAULT NULL,
    originating_vasp_id BIGINT check (originating_vasp_id > 0) DEFAULT NULL,
    originator_account_number nvarchar(256) DEFAULT NULL,
    ivms101_originator nvarchar(MAX) DEFAULT NULL,
    ivms101_originating_vasp nvarchar(MAX) DEFAULT NULL,
    beneficiary_vasp_id BIGINT check (beneficiary_vasp_id > 0) DEFAULT NULL,
    beneficiary_account_number nvarchar(256) DEFAULT NULL,
    ivms101_beneficiary nvarchar(MAX) DEFAULT NULL,
    ivms101_beneficiary_vasp nvarchar(MAX) DEFAULT NULL,
    verified_at datetime2(3) DEFAULT NULL,
    ordered_at datetime2(3) DEFAULT NULL,
    created_at datetime2(3) DEFAULT CURRENT_TIMESTAMP
    );

    CREATE INDEX idx_verifications_originator_account ON verifications(originator_account_number);
    CREATE INDEX idx_verifications_beneficiary_account ON verifications(beneficiary_account_number);
    ```
  </Tab>

  <Tab title="Oracle">
    ```sql
    CREATE TABLE "verifications" (
    "verification_id" number(20) NOT NULL,
    "verification_uuid" varchar2(40) NOT NULL,
    "result" varchar2(20) DEFAULT 'WAIT' NOT NULL check ("result" in ('WAIT', 'VERIFIED', 'UNKNOWN', 'DENIED', 'ERROR', 'PENDING', 'TRANSFER_ERROR')),
    "reason" varchar2(256) DEFAULT NULL,
    "message" varchar2(1024) DEFAULT NULL,
    "network" varchar2(128) DEFAULT NULL,
    "symbol" varchar2(16) DEFAULT NULL,
    "amount" varchar2(128) DEFAULT NULL,
    "trade_price" varchar2(128) DEFAULT NULL,
    "trade_currency" varchar2(128) DEFAULT NULL,
    "trade_iso_datetime" date DEFAULT NULL,
    "is_exceeding_threshold" number(1) DEFAULT 1,
    "tx_hash" varchar2(128) DEFAULT NULL,
    "vout" varchar2(128) DEFAULT NULL,
    "originating_vasp_id" varchar2(20) DEFAULT NULL,
    "originator_account_number" varchar2(256) DEFAULT NULL,
    "ivms101_originator" clob DEFAULT NULL,
    "ivms101_originating_vasp" clob DEFAULT NULL,
    "beneficiary_vasp_id" varchar2(20) DEFAULT NULL,
    "beneficiary_account_number" varchar2(256) DEFAULT NULL,
    "ivms101_beneficiary" clob DEFAULT NULL,
    "ivms101_beneficiary_vasp" clob DEFAULT NULL,
    "verified_at" timestamp(3) DEFAULT NULL,
    "ordered_at" timestamp(3) DEFAULT NULL,
    "created_at" timestamp(3) DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT "pk_verification_id" PRIMARY KEY ("verification_id"),
    CONSTRAINT "uk_verification_uuid" UNIQUE("verification_uuid")
    );

    CREATE INDEX "idx_originator_account" ON "verifications"("originator_account_number");
    CREATE INDEX "idx_beneficiary_account" ON "verifications"("beneficiary_account_number");

    CREATE SEQUENCE "verifications_id_seq";

    CREATE OR REPLACE TRIGGER verifications_trigger
    BEFORE INSERT ON "verifications"
    FOR EACH ROW
    BEGIN
    SELECT "verifications_id_seq".nextval
    INTO :new."verification_id"
    FROM dual;
    END;
    ```
  </Tab>
</Tabs>

<br />

## Configuration Query for Optional Table: Chainalysis Sanction Related Tables

If your VASP utilizes the Chainalysis Sanction feature, execute the following queries to set up the corresponding database.

<Tabs>
  <Tab title="MySQL">
    ```sql
    CREATE TABLE `chainalysis_sanction_results` (
    `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `request_id` varchar(40) NOT NULL,
    `verification_uuid` varchar(40) NOT NULL,
    `counterparty_vasp_id` bigint(20) unsigned NOT NULL,
    `direction` enum('OUTGOING', 'INCOMING') NOT NULL,
    `address` varchar(512) NOT NULL,
    `status` enum('NOHIT', 'SANCTION', 'CLOSED', 'ERROR') NOT NULL,
    `ofac_name` varchar(1024) DEFAULT NULL,
    `ofac_description` varchar(4096) DEFAULT NULL,
    `ofac_url` varchar(1024) DEFAULT NULL,
    `created_at` datetime(3) DEFAULT CURRENT_TIMESTAMP(3),
    `updated_at` datetime(3) DEFAULT CURRENT_TIMESTAMP(3),
    PRIMARY KEY (`id`),
    UNIQUE KEY `uniq_request_id` (`request_id`),
    INDEX `idx_counterparty_vasp_id` (`counterparty_vasp_id`, `created_at`),
    INDEX `idx_address` (`address`, `created_at`),
    INDEX `idx_created_at` (`created_at`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
    ```
  </Tab>

  <Tab title="PostgreSQL">
    ```sql
    CREATE TYPE enum_direction AS ENUM ('OUTGOING', 'INCOMING');
    CREATE TYPE enum_chainalysis_sanction_status AS ENUM ('NOHIT', 'SANCTION', 'CLOSED', 'ERROR');

    CREATE TABLE chainalysis_sanction_results (
    id SERIAL NOT NULL PRIMARY KEY,
    request_id varchar(40) NOT NULL,
    verification_uuid varchar(40) NOT NULL,
    counterparty_vasp_id numeric(20) NOT NULL,
    direction enum_direction NOT NULL,
    address varchar(512) NOT NULL,
    status enum_chainalysis_sanction_status NOT NULL,
    ofac_name varchar(1024) DEFAULT NULL,
    ofac_description varchar(4096) DEFAULT NULL,
    ofac_url varchar(1024) DEFAULT NULL,
    created_at timestamp DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT uniq_sanction_request_id UNIQUE (request_id)
    );

    CREATE INDEX idx_chainalysis_sanction_results_counterparty_vasp_id ON chainalysis_sanction_results(counterparty_vasp_id, created_at);
    CREATE INDEX idx_chainalysis_sanction_results_address ON chainalysis_sanction_results(address, created_at);
    CREATE INDEX idx_chainalysis_sanction_results_created_at ON chainalysis_sanction_results(created_at);
    ```
  </Tab>

  <Tab title="MSSQL">
    ```sql
    CREATE TABLE chainalysis_sanction_results (
    id BIGINT IDENTITY(1,1) NOT NULL PRIMARY KEY,
    request_id nvarchar(40) NOT NULL,
    verification_uuid nvarchar(40) NOT NULL,
    counterparty_vasp_id BIGINT NOT NULL,
    direction nvarchar(20) NOT NULL check (direction in ('OUTGOING', 'INCOMING')),
    address nvarchar(512) NOT NULL,
    status nvarchar(20) NOT NULL check (status in ('NOHIT', 'SANCTION', 'CLOSED', 'ERROR')),
    ofac_name nvarchar(1024) DEFAULT NULL,
    ofac_description nvarchar(4000) DEFAULT NULL,
    ofac_url nvarchar(1024) DEFAULT NULL,
    created_at datetime2(3) DEFAULT CURRENT_TIMESTAMP,
    updated_at datetime2(3) DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT key_uniq_sanction_request_id UNIQUE (request_id)
    );

    CREATE INDEX idx_chainalysis_sanction_results_counterparty_vasp_id ON chainalysis_sanction_results(counterparty_vasp_id, created_at);
    CREATE INDEX idx_chainalysis_sanction_results_address ON chainalysis_sanction_results(address, created_at);
    CREATE INDEX idx_chainalysis_sanction_results_created_at ON chainalysis_sanction_results(created_at);
    ```
  </Tab>

  <Tab title="Oracle">
    ```sql
    CREATE TABLE "chainalysis_sanction_results" (
    "id" number(20) NOT NULL,
    "request_id" varchar2(40) NOT NULL,
    "verification_uuid" varchar2(40) NOT NULL,
    "counterparty_vasp_id" varchar2(20) NOT NULL,
    "direction" varchar2(20) NOT NULL CHECK ("direction" IN ('OUTGOING', 'INCOMING')),
    "address" varchar2(512) NOT NULL,
    "status" varchar2(20) NOT NULL CHECK ("status" IN ('NOHIT', 'SANCTION', 'CLOSED', 'ERROR')),
    "ofac_name" varchar2(1024) DEFAULT NULL,
    "ofac_description" varchar2(2048) DEFAULT NULL,
    "ofac_url" varchar2(1024) DEFAULT NULL,
    "created_at" timestamp(3) DEFAULT CURRENT_TIMESTAMP,
    "updated_at" timestamp(3) DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT "chainalysis_sanction_pk" PRIMARY KEY ("id"),
    CONSTRAINT "uniq_sanction_request_id" UNIQUE ("request_id")
    );

    CREATE INDEX "idx_counterparty_vasp_id" ON "chainalysis_sanction_results" ("counterparty_vasp_id", "created_at");
    CREATE INDEX "idx_address" ON "chainalysis_sanction_results" ("address", "created_at");
    CREATE INDEX "idx_created_at" ON "chainalysis_sanction_results" ("created_at");

    CREATE SEQUENCE "chainalysis_sanction_seq";

    -- Create a trigger
    CREATE OR REPLACE TRIGGER chainalysis_sanction_trg
    BEFORE INSERT ON "chainalysis_sanction_results"
    FOR EACH ROW
    BEGIN
    SELECT "chainalysis_sanction_seq".NEXTVAL
    INTO :new."id"
    FROM dual;
    END;
    ```
  </Tab>
</Tabs>

## Configuration Query for Optional Tables: Chainalysis KYT Related Tables

If your VASP utilizes the Chainalysis KYT feature, execute the following queries to set up the corresponding database.

<Tabs>
  <Tab title="MySQL">
    ```sql
    CREATE TABLE `chainalysis_kyt_results` (
    `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `request_id` varchar(40) NOT NULL,
    `verification_uuid` varchar(40) NOT NULL,
    `counterparty_vasp_id` bigint(20) unsigned NOT NULL,
    `api_type` enum('TRANSFER', 'ATTEMPT') NOT NULL,
    `user_id` varchar(128) NOT NULL,
    `direction` enum('OUTGOING', 'INCOMING') NOT NULL,
    `network` varchar(128) DEFAULT NULL,
    `asset` varchar(16) NOT NULL,
    `amount` varchar(128) NOT NULL,
    `usd_price` varchar(128) DEFAULT NULL,
    `transfer_ref` varchar(1024) DEFAULT NULL,
    `output_address` varchar(512) DEFAULT NULL,
    `timestamp` datetime(3) DEFAULT NULL,
    `external_id` varchar(128) DEFAULT NULL,
    `status` enum('ERROR', 'REGISTERED', 'CHECKING', 'PROCESSED') NOT NULL,
    `worker_id` varchar(128) DEFAULT NULL,
    `last_checked_at` datetime(3) DEFAULT NULL,
    `alert_count` int(11) DEFAULT NULL,
    `created_at` datetime(3) DEFAULT CURRENT_TIMESTAMP(3),
    `updated_at` datetime(3) DEFAULT CURRENT_TIMESTAMP(3),
    `assessed_at` datetime(3) DEFAULT NULL,
    PRIMARY KEY (`id`),
    UNIQUE KEY `uniq_request_id` (`request_id`),
    INDEX `idx_external_id` (`external_id`),
    INDEX `idx_counterparty_vasp_id` (`counterparty_vasp_id`, `created_at`),
    INDEX `idx_direction` (`direction`, `created_at`),
    INDEX `idx_status_last_checked_at` (`status`, `last_checked_at`),
    INDEX `idx_status_worker_id` (`status`, `worker_id`, `id`),
    INDEX `idx_created_at` (`created_at`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;

    CREATE TABLE `chainalysis_kyt_alerts` (
    `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `external_id` varchar(128) NOT NULL,
    `alert_id` varchar(128) NOT NULL,
    `alert_level` enum('LOW', 'MEDIUM', 'HIGH', 'SEVERE') NOT NULL,
    `entity_category` varchar(256) DEFAULT NULL,
    `service_name` varchar(256) DEFAULT NULL,
    `exposure_type` enum('DIRECT', 'INDIRECT') NOT NULL,
    `alert_amount` varchar(128) NOT NULL,
    `created_at` datetime(3) DEFAULT CURRENT_TIMESTAMP(3),
    `updated_at` datetime(3) DEFAULT CURRENT_TIMESTAMP(3),
    PRIMARY KEY (`id`),
    UNIQUE KEY `uniq_alert_id` (`alert_id`),
    INDEX `idx_external_id` (`external_id`),
    INDEX `idx_alert_level` (`alert_level`),
    INDEX `idx_created_at` (`created_at`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
    ```
  </Tab>

  <Tab title="PostgreSQL">
    ```sql
    CREATE TYPE enum_api_type AS ENUM('TRANSFER', 'ATTEMPT');
    CREATE TYPE enum_chainalysis_kyt_status AS ENUM('ERROR', 'REGISTERED', 'CHECKING', 'PROCESSED');

    CREATE TABLE chainalysis_kyt_results (
    id SERIAL NOT NULL PRIMARY KEY,
    request_id varchar(40) NOT NULL,
    verification_uuid varchar(40) NOT NULL,
    counterparty_vasp_id numeric(20) NOT NULL,
    api_type enum_api_type NOT NULL,
    user_id varchar(128) NOT NULL,
    direction enum_direction NOT NULL,
    network varchar(128) DEFAULT NULL,
    asset varchar(16) NOT NULL,
    amount varchar(128) NOT NULL,
    usd_price varchar(128) DEFAULT NULL,
    transfer_ref varchar(1024) DEFAULT NULL,
    output_address varchar(512) DEFAULT NULL,
    timestamp timestamp DEFAULT NULL,
    external_id varchar(128) DEFAULT NULL,
    status enum_chainalysis_kyt_status NOT NULL,
    worker_id varchar(128) DEFAULT NULL,
    last_checked_at timestamp DEFAULT NULL,
    alert_count numeric(11) DEFAULT NULL,
    created_at timestamp DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp DEFAULT CURRENT_TIMESTAMP,
    assessed_at timestamp DEFAULT NULL,
    CONSTRAINT uniq_kyt_request_id UNIQUE (request_id)
    );

    CREATE INDEX idx_chainalysis_kyt_results_external_id ON chainalysis_kyt_results(external_id);
    CREATE INDEX idx_chainalysis_kyt_results_counterparty_vasp_id ON chainalysis_kyt_results(counterparty_vasp_id, created_at);
    CREATE INDEX idx_chainalysis_kyt_results_direction ON chainalysis_kyt_results(direction, created_at);
    CREATE INDEX idx_chainalysis_kyt_results_status_last_checked_at ON chainalysis_kyt_results(status, last_checked_at);
    CREATE INDEX idx_chainalysis_kyt_results_status_worker_id ON chainalysis_kyt_results(status, worker_id, id);
    CREATE INDEX idx_chainalysis_kyt_results_created_at ON chainalysis_kyt_results(created_at);

    CREATE TYPE enum_alert_level AS ENUM ('LOW', 'MEDIUM', 'HIGH', 'SEVERE');
    CREATE TYPE enum_exposure_type AS ENUM('DIRECT', 'INDIRECT');

    CREATE TABLE chainalysis_kyt_alerts (
    id SERIAL NOT NULL PRIMARY KEY,
    external_id varchar(128) NOT NULL,
    alert_id varchar(128) NOT NULL UNIQUE,
    alert_level enum_alert_level NOT NULL,
    entity_category varchar(256) DEFAULT NULL,
    service_name varchar(256) DEFAULT NULL,
    exposure_type enum_exposure_type NOT NULL,
    alert_amount varchar(128) NOT NULL,
    created_at timestamp DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp DEFAULT CURRENT_TIMESTAMP
    );

    CREATE INDEX idx_chainalysis_kyt_alerts_external_id ON chainalysis_kyt_alerts(external_id);
    CREATE INDEX idx_chainalysis_kyt_alerts_alert_level ON chainalysis_kyt_alerts(alert_level);
    CREATE INDEX idx_chainalysis_kyt_alerts_created_at ON chainalysis_kyt_alerts(created_at);
    ```
  </Tab>

  <Tab title="MSSQL">
    ```sql
    CREATE TABLE chainalysis_kyt_results (
    id BIGINT IDENTITY(1,1) NOT NULL PRIMARY KEY,
    request_id nvarchar(40) NOT NULL,
    verification_uuid nvarchar(40) NOT NULL,
    counterparty_vasp_id BIGINT NOT NULL,
    api_type nvarchar(10) NOT NULL check (api_type in ('TRANSFER', 'ATTEMPT')),
    user_id nvarchar(128) NOT NULL,
    direction nvarchar(10) NOT NULL check (direction in ('OUTGOING', 'INCOMING')),
    network nvarchar(128) DEFAULT NULL,
    asset nvarchar(16) NOT NULL,
    amount nvarchar(128) NOT NULL,
    usd_price nvarchar(128) DEFAULT NULL,
    transfer_ref nvarchar(1024) DEFAULT NULL,
    output_address nvarchar(512) DEFAULT NULL,
    timestamp datetime2(3) DEFAULT NULL,
    external_id nvarchar(128) DEFAULT NULL,
    status nvarchar(20) NOT NULL check (status in ('ERROR', 'REGISTERED', 'CHECKING', 'PROCESSED')),
    worker_id nvarchar(128) DEFAULT NULL,
    last_checked_at datetime2(3) DEFAULT NULL,
    alert_count int DEFAULT NULL,
    created_at datetime2(3) DEFAULT CURRENT_TIMESTAMP,
    updated_at datetime2(3) DEFAULT CURRENT_TIMESTAMP,
    assessed_at datetime2(3) DEFAULT NULL,
    CONSTRAINT key_uniq_kyt_request_id UNIQUE (request_id)
    );

    CREATE INDEX idx_chainalysis_kyt_results_external_id ON chainalysis_kyt_results(external_id);
    CREATE INDEX idx_chainalysis_kyt_results_counterparty_vasp_id ON chainalysis_kyt_results(counterparty_vasp_id, created_at);
    CREATE INDEX idx_chainalysis_kyt_results_direction ON chainalysis_kyt_results(direction, created_at);
    CREATE INDEX idx_chainalysis_kyt_results_status_last_checked_at ON chainalysis_kyt_results(status, last_checked_at);
    CREATE INDEX idx_chainalysis_kyt_results_status_worker_id ON chainalysis_kyt_results(status, worker_id, id);
    CREATE INDEX idx_chainalysis_kyt_results_created_at ON chainalysis_kyt_results(created_at);

    CREATE TABLE chainalysis_kyt_alerts (
    id BIGINT IDENTITY(1,1) NOT NULL PRIMARY KEY,
    external_id nvarchar(128) NOT NULL,
    alert_id nvarchar(128) NOT NULL UNIQUE,
    alert_level nvarchar(20) NOT NULL check (alert_level in ('LOW', 'MEDIUM', 'HIGH', 'SEVERE')),
    entity_category nvarchar(256) DEFAULT NULL,
    service_name nvarchar(256) DEFAULT NULL,
    exposure_type nvarchar(20) NOT NULL check (exposure_type in ('DIRECT', 'INDIRECT')),
    alert_amount nvarchar(128) NOT NULL,
    created_at datetime2(3) DEFAULT CURRENT_TIMESTAMP,
    updated_at datetime2(3) DEFAULT CURRENT_TIMESTAMP,
    );

    CREATE INDEX idx_chainalysis_kyt_alerts_external_id ON chainalysis_kyt_alerts(external_id);
    CREATE INDEX idx_chainalysis_kyt_alerts_alert_level ON chainalysis_kyt_alerts(alert_level);
    CREATE INDEX idx_chainalysis_kyt_alerts_created_at ON chainalysis_kyt_alerts(created_at);
    ```
  </Tab>

  <Tab title="Oracle">
    ```sql
    CREATE TABLE "chainalysis_kyt_results" (
    "id" number(20) NOT NULL,
    "request_id" varchar2(40) NOT NULL,
    "verification_uuid" varchar2(40) NOT NULL,
    "counterparty_vasp_id" varchar2(20) NOT NULL,
    "api_type" varchar2(20) NOT NULL CHECK ("api_type" IN ('TRANSFER', 'ATTEMPT')),
    "user_id" varchar2(128) NOT NULL,
    "direction" varchar2(20) NOT NULL CHECK ("direction" IN ('OUTGOING', 'INCOMING')),
    "network" varchar2(128) DEFAULT NULL,
    "asset" varchar2(16) NOT NULL,
    "amount" varchar2(128) NOT NULL,
    "usd_price" varchar2(128) DEFAULT NULL,
    "transfer_ref" varchar2(1024) DEFAULT NULL,
    "output_address" varchar2(512) DEFAULT NULL,
    "timestamp" timestamp(3) DEFAULT NULL,
    "external_id" varchar2(128) DEFAULT NULL,
    "status" varchar2(20) NOT NULL CHECK ("status" IN ('ERROR', 'REGISTERED', 'CHECKING', 'PROCESSED')),
    "worker_id" varchar2(128) DEFAULT NULL,
    "last_checked_at" timestamp(3) DEFAULT NULL,
    "alert_count" number(11) DEFAULT NULL,
    "created_at" timestamp(3) DEFAULT CURRENT_TIMESTAMP,
    "updated_at" timestamp(3) DEFAULT CURRENT_TIMESTAMP,
    "assessed_at" timestamp(3) DEFAULT NULL,
    CONSTRAINT "chainalysis_kyt_results_pk" PRIMARY KEY ("id"),
    CONSTRAINT "uniq_kyt_request_id" UNIQUE ("request_id")
    );

    CREATE INDEX "idx_kyt_external_id" ON "chainalysis_kyt_results" ("external_id");
    CREATE INDEX "idx_kyt_counterparty_vasp_id" ON "chainalysis_kyt_results" ("counterparty_vasp_id", "created_at");
    CREATE INDEX "idx_kyt_direction" ON "chainalysis_kyt_results" ("direction", "created_at");
    CREATE INDEX "idx_kyt_status_last_checked_at" ON "chainalysis_kyt_results" ("status", "last_checked_at");
    CREATE INDEX "idx_kyt_status_worker_id" ON "chainalysis_kyt_results" ("status", "worker_id", "id");
    CREATE INDEX "idx_kyt_created_at" ON "chainalysis_kyt_results" ("created_at");

    -- Create a sequence
    CREATE SEQUENCE "chainalysis_kyt_results_seq";

    -- Create a trigger
    CREATE OR REPLACE TRIGGER chainalysis_kyt_results_trg
    BEFORE INSERT ON "chainalysis_kyt_results"
    FOR EACH ROW
    BEGIN
    SELECT "chainalysis_kyt_results_seq".NEXTVAL
    INTO :new."id"
    FROM dual;
    END;

    CREATE TABLE "chainalysis_kyt_alerts" (
    "id" number(20) NOT NULL,
    "external_id" varchar2(128) NOT NULL,
    "alert_id" varchar2(128) NOT NULL,
    "alert_level" varchar2(20) NOT NULL CHECK ("alert_level" IN ('LOW', 'MEDIUM', 'HIGH', 'SEVERE')),
    "entity_category" varchar2(256) DEFAULT NULL,
    "service_name" varchar2(256) DEFAULT NULL,
    "exposure_type" varchar2(20) NOT NULL CHECK ("exposure_type" IN ('DIRECT', 'INDIRECT')),
    "alert_amount" varchar2(128) NOT NULL,
    "created_at" timestamp(3) DEFAULT CURRENT_TIMESTAMP,
    "updated_at" timestamp(3) DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT "chainalysis_kyt_alerts_pk" PRIMARY KEY ("id"),
    CONSTRAINT "uniq_alert_id" UNIQUE ("alert_id")
    );

    CREATE INDEX "idx_kyta_external_id" ON "chainalysis_kyt_alerts" ("external_id");
    CREATE INDEX "idx_kyta_alert_level" ON "chainalysis_kyt_alerts" ("alert_level");
    CREATE INDEX "idx_kyta_created_at" ON "chainalysis_kyt_alerts" ("created_at");

    -- Create a sequence
    CREATE SEQUENCE "chainalysis_kyt_alerts_seq";

    -- Create a trigger
    CREATE OR REPLACE TRIGGER chainalysis_kyt_alerts_trg
    BEFORE INSERT ON "chainalysis_kyt_alerts"
    FOR EACH ROW
    BEGIN
    SELECT "chainalysis_kyt_alerts_seq".NEXTVAL
    INTO :new."id"
    FROM dual;
    END;
    ```
  </Tab>
</Tabs>

## Configuration Query for Optional Table: Refinitiv WCO Related Tables

If your VASP utilizes the Refinitiv WCO feature, execute the following queries to set up the corresponding database.

<Tabs>
  <Tab title="MySQL">
    ```sql
    CREATE TABLE `refinitiv_wco_results` (
    `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `request_id` varchar(40) NOT NULL,
    `verification_uuid` varchar(40) NOT NULL,
    `counterparty_vasp_id` bigint(20) unsigned NOT NULL,
    `direction` enum('OUTGOING', 'INCOMING') NOT NULL,
    `case_system_id` varchar(128) NOT NULL,
    `aggregated_result_summaries` varchar(4096) DEFAULT NULL,
    `worker_id` varchar(128) DEFAULT NULL,
    `status` enum('ERROR', 'REGISTERED', 'CHECKING', 'PROCESSED') NOT NULL,
    `last_checked_at` datetime(3) DEFAULT NULL,
    `created_at` datetime(3) DEFAULT CURRENT_TIMESTAMP(3),
    `updated_at` datetime(3) DEFAULT CURRENT_TIMESTAMP(3),
    `assessed_at` datetime(3) DEFAULT NULL,
    PRIMARY KEY (`id`),
    UNIQUE KEY `uniq_request_id` (`request_id`),
    INDEX `idx_counterparty_vasp_id` (`counterparty_vasp_id`, `created_at`),
    INDEX `idx_direction` (`direction`, `created_at`),
    INDEX `idx_status_last_checked_at` (`status`, `last_checked_at`),
    INDEX `idx_status_worker_id` (`status`, `worker_id`, `id`),
    INDEX `idx_created_at` (`created_at`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
    ```
  </Tab>

  <Tab title="PostgreSQL">
    ```sql
    CREATE TYPE enum_refinitiv_wco_status AS ENUM('ERROR', 'REGISTERED', 'CHECKING', 'PROCESSED');

    CREATE TABLE refinitiv_wco_results (
    id SERIAL NOT NULL PRIMARY KEY,
    request_id varchar(40) NOT NULL,
    verification_uuid varchar(40) NOT NULL,
    counterparty_vasp_id numeric(20) NOT NULL,
    direction enum_direction NOT NULL,
    case_system_id varchar(128) NOT NULL,
    aggregated_result_summaries varchar(4096) DEFAULT NULL,
    worker_id varchar(128) DEFAULT NULL,
    status enum_refinitiv_wco_status NOT NULL,
    last_checked_at timestamp DEFAULT NULL,
    created_at timestamp DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp DEFAULT CURRENT_TIMESTAMP,
    assessed_at timestamp DEFAULT NULL,
    CONSTRAINT key_uniq_wco_request_id UNIQUE (request_id)
    );

    CREATE INDEX idx_refinitiv_wco_results_counterparty_vasp_id ON refinitiv_wco_results(counterparty_vasp_id, created_at);
    CREATE INDEX idx_refinitiv_wco_results_direction ON refinitiv_wco_results(direction, created_at);
    CREATE INDEX idx_refinitiv_wco_results_status_last_checked_at ON refinitiv_wco_results(status, last_checked_at);
    CREATE INDEX idx_refinitiv_wco_results_status_worker_id ON refinitiv_wco_results(status, worker_id, id);
    CREATE INDEX idx_refinitiv_wco_results_created_at ON refinitiv_wco_results(created_at);
    ```
  </Tab>

  <Tab title="MSSQL">
    ```sql
    CREATE TABLE refinitiv_wco_results (
    id BIGINT IDENTITY(1,1) NOT NULL PRIMARY KEY,
    request_id nvarchar(40) NOT NULL,
    verification_uuid nvarchar(40) NOT NULL,
    counterparty_vasp_id BIGINT NOT NULL,
    direction nvarchar(20) NOT NULL check (direction in ('OUTGOING', 'INCOMING')),
    case_system_id nvarchar(128) NOT NULL,
    aggregated_result_summaries nvarchar(4000) DEFAULT NULL,
    worker_id nvarchar(128) DEFAULT NULL,
    status nvarchar(20) NOT NULL check (status in ('ERROR', 'REGISTERED', 'CHECKING', 'PROCESSED')),
    last_checked_at datetime2(3) DEFAULT NULL,
    created_at datetime2(3) DEFAULT CURRENT_TIMESTAMP,
    updated_at datetime2(3) DEFAULT CURRENT_TIMESTAMP,
    assessed_at datetime2(3) DEFAULT NULL,
    CONSTRAINT key_uniq_wco_request_id UNIQUE (request_id)
    );

    CREATE INDEX idx_refinitiv_wco_results_counterparty_vasp_id ON refinitiv_wco_results(counterparty_vasp_id, created_at);
    CREATE INDEX idx_refinitiv_wco_results_direction ON refinitiv_wco_results(direction, created_at);
    CREATE INDEX idx_refinitiv_wco_results_status_last_checked_at ON refinitiv_wco_results(status, last_checked_at);
    CREATE INDEX idx_refinitiv_wco_results_status_worker_id ON refinitiv_wco_results(status, worker_id, id);
    CREATE INDEX idx_refinitiv_wco_results_created_at ON refinitiv_wco_results(created_at);
    ```
  </Tab>

  <Tab title="Oracle">
    ```sql
    CREATE TABLE "refinitiv_wco_results" (
    "id" number(20) NOT NULL,
    "request_id" varchar2(40) NOT NULL,
    "verification_uuid" varchar2(40) NOT NULL,
    "counterparty_vasp_id" varchar2(20) NOT NULL,
    "direction" varchar2(20) NOT NULL CHECK ("direction" IN ('OUTGOING', 'INCOMING')),
    "case_system_id" varchar2(128) NOT NULL,
    "aggregated_result_summaries" varchar2(2048) DEFAULT NULL,
    "worker_id" varchar2(128) DEFAULT NULL,
    "status" varchar2(20) NOT NULL CHECK ("status" IN ('ERROR', 'REGISTERED', 'CHECKING', 'PROCESSED')),
    "last_checked_at" timestamp(3) DEFAULT NULL,
    "created_at" timestamp(3) DEFAULT CURRENT_TIMESTAMP,
    "updated_at" timestamp(3) DEFAULT CURRENT_TIMESTAMP,
    "assessed_at" timestamp(3) DEFAULT NULL,
    CONSTRAINT "refinitiv_wco_results_pk" PRIMARY KEY ("id"),
    CONSTRAINT "uniq_wco_request_id" UNIQUE ("request_id")
    );

    CREATE INDEX "idx_wco_counterparty_vasp_id" ON "refinitiv_wco_results" ("counterparty_vasp_id", "created_at");
    CREATE INDEX "idx_wco_direction" ON "refinitiv_wco_results" ("direction", "created_at");
    CREATE INDEX "idx_wco_status_last_checked_at" ON "refinitiv_wco_results" ("status", "last_checked_at");
    CREATE INDEX "idx_wco_status_worker_id" ON "refinitiv_wco_results" ("status", "worker_id", "id");
    CREATE INDEX "idx_wco_created_at" ON "refinitiv_wco_results" ("created_at");

    -- Create a sequence
    CREATE SEQUENCE "refinitiv_wco_results_seq";

    -- Create a trigger
    CREATE OR REPLACE TRIGGER refinitiv_wco_results_trg
    BEFORE INSERT ON "refinitiv_wco_results"
    FOR EACH ROW
    BEGIN
    SELECT "refinitiv_wco_results_seq".NEXTVAL
    INTO :new."id"
    FROM dual;
    END;
    ```
  </Tab>
</Tabs>