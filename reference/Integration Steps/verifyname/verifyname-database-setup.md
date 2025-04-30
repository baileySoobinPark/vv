---
title: Database Setup
deprecated: false
hidden: true
metadata:
  robots: index
---
## Tables

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
        owner\_verifications
      </td>

      <td>
        * A table for storing owner verification status and history. When **POST v2/owner-verifications API** is called, the request/response result will be recorded in the owner\_verifications table.
        * Following columns are encrypted.
          ◦ party\_info
          ◦ party\_info\_hash
        * • Consider that each record in this table will consume approximately **4–5 KB per verification**.
      </td>

      <td>
        **Daily backup** is recommended as the table stores the owner verification history.
      </td>
    </tr>
  </tbody>
</Table>

<br />

## Table Creation Query

* Enclave mode 를 VN2 로 설정한 경우 데이터 베이스 유형에 적합한 테이블 생성 및 키 구성 쿼리를 실행하여 데이터 베이스 설정이 필요합니다.

* MySQL

  ```json
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
    `results` varchar(4000) DEFAULT NULL,
    `reported_result` varchar(128) DEFAULT NULL,
    `status` varchar(40) NOT NULL,
    `reason` varchar(256) DEFAULT NULL,
    `message` varchar(1024) DEFAULT NULL,
    `verified_at` datetime(3) DEFAULT NULL,
    `ordered_at` datetime(3) DEFAULT NULL,
    `updated_at` datetime(3) DEFAULT CURRENT_TIMESTAMP(3) ON UPDATE CURRENT_TIMESTAMP(3),
    `created_at` datetime(3) DEFAULT CURRENT_TIMESTAMP(3),
    PRIMARY KEY (`verification_id`),
    UNIQUE KEY `uk_owner_verifications_request_id` (`request_id`),
    KEY `idx_request_vasp_id_response_vasp_id` (`request_vasp_id`,`response_vasp_id`)
  ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
  ```

* PostgreSQL

  ```json
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
    results varchar(4000) DEFAULT NULL,
    reported_result varchar(128) DEFAULT NULL,
    status varchar(40) NOT NULL,
    reason varchar(256) DEFAULT NULL,
    message varchar(1024) DEFAULT NULL,
    verified_at timestamp DEFAULT NULL,
    ordered_at timestamp DEFAULT NULL,
    updated_at timestamp DEFAULT CURRENT_TIMESTAMP,
    created_at timestamp DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT key_uniq_owner_verifications_request_id UNIQUE (request_id)
  );

  CREATE INDEX idx_request_vasp_id_response_vasp_id ON owner_verifications(request_vasp_id, response_vasp_id);
  ```

* MSSQL

  ```json
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
  	results nvarchar(4000) DEFAULT NULL,
  	reported_result nvarchar(128) DEFAULT NULL,
  	status nvarchar(40) NOT NULL,
  	reason nvarchar(256) DEFAULT NULL,
  	message nvarchar(1024) DEFAULT NULL,
  	verified_at datetime2(3) DEFAULT NULL,
  	ordered_at datetime2(3) DEFAULT NULL,
  	updated_at datetime2(3) DEFAULT CURRENT_TIMESTAMP,
  	created_at datetime2(3) DEFAULT CURRENT_TIMESTAMP,
  );

  CREATE INDEX idx_request_vasp_id_response_vasp_id ON owner_verifications(request_vasp_id, response_vasp_id);
  ```

* Oracle

  ```json
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
   "results" varchar2(4000) DEFAULT NULL,
   "reported_result" varchar2(128) DEFAULT NULL,
   "status" varchar2(40) NOT NULL,
   "reason" varchar2(256) DEFAULT NULL,
   "message" varchar2(1024) DEFAULT NULL,
   "verified_at" timestamp(3) DEFAULT NULL,
   "ordered_at" timestamp(3) DEFAULT NULL,
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