---
title: Enclave 데이터베이스 생성
excerpt: >-
  VASP API 구현이 완료되었다면 Enclave 서버를 구동하기 위한 준비를 시작합니다. Enclave 서버를 구동하기에 앞서,
  Enclave가 사용할 데이터베이스를 사용 가능한 상태로 사전 구성해야 합니다. 본 가이드는 Enclave 서버에서 사용할 데이터베이스
  설정을 위한 시스템 요구사항, 운영 정책, 설정 항목들을 상세히 설명합니다.
deprecated: false
hidden: false
metadata:
  robots: index
---
<Common_database />

## Step 1. DBMS 선택 및 설치

VerifyVASP Enclave 데이터베이스는 Enclave의 검증 결과, 트랜잭션 로그, 상대 VASP의 공개 키 등 주요 정보를 저장하기 위해 사용됩니다. 아래 지원 DBMS 중 귀사의 인프라와 운영 환경에 적합한 DBMS를 선택하십시오.

### 지원하는 DBMS

* **MySQL`Recommended`**
  * 설치 및 운영이 간편하고 성능이 우수한 MySQL 사용을 권장합니다.
* **PostgreSQL**
* **MSSQL**
* **Oracle DBMS**

## Step 2. 스키마 생성

DBMS 설치가 완료되면, Enclave가 사용할 테이블들을 생성해야 합니다. 전체 스키마는 필수 테이블 4개와 선택 테이블 4개로 구성됩니다. 선택 테이블은 Chainalysis, Refinitiv 등 3rd Party를 통한 Screening 프로세스를 사용하는 경우에만 필요합니다. 각 테이블에 대한 상세 설명은 아래 표와 같습니다.

> ⚠️ 저장소 크기 및 백업 정책 유의사항
>
> 각 테이블 설명에 포함된 예상 레코드 크기를 참고하여, 예상 요청량에 맞는 충분한 저장소를 사전에 확보하시기 바랍니다. 또한, 장기적인 데이터 무결성과 안정적인 운영을 위해 백업 및 복구 정책을 반드시 적용해 주십시오.

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
    background-color: #ffffff;
    font-weight: 600;
  }

  .custom-table td {
    background-color: #ffffff;
  }

  .custom-table td.code-col {
    min-width: 200px;
    white-space: nowrap;
    font-family: monospace;
  }
</style>

<table class="custom-table">
  <thead>
    <tr>
      <th>Table</th>
      <th>Description</th>
      <th>Backup Policy</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="code-col"><code>own_keys</code></td>
      <td>
        - <b>필수 테이블입니다.</b> <br>
        - 귀사 VASP Enclave의 공개키/비밀키 쌍을 저장합니다.<br>
        - 검증 중 상대 VASP의 공개키 요청시 본 테이블로부터 조회 및 반환됩니다.<br>
        - 암호화 대상 필드: <code>private_key</code><br>
        - 레코드당 예상크키는 약 <strong>1 KB</strong>입니다.
      </td>
      <td>주기적인 백업 권장</td>
    </tr>
    <tr>
      <td class="code-col"><code>counter_party_keys</code></td>
      <td>
        - <b>필수 테이블입니다.</b> <br>
        - 상대 VASP의 공개키를 캐싱하여 저장합니다.<br>
        - 레코드당 예상크키는 약 <strong>1 KB</strong>입니다.
      </td>
      <td>백업 또는 복원 정책 불필요</td>
    </tr>
    <tr>
      <td class="code-col"><code>commands</code></td>
      <td>
        - <b>필수 테이블입니다.</b> <br>
        - Enclave 내부적으로 비동기 API의 중간 처리 상태를 저장합니다.<br> 
        - 레코드당 예상크키는 약 <strong>1-5 KB</strong>입니다.
      </td>
      <td>백업 또는 복원 정책 불필요</td>
    </tr>
		<tr>
      <td class="code-col"><code>owner_verifications</code></td>
      <td>
        - <b>필수 테이블입니다.</b> <br>
        - 계좌 소유주 검증 요청 및 결과 이력을 저장합니다.<br> 
 				- POST /v2/owner-verifications API 호출시 요청과 응답 데이터가 저장됩니다.
        - 암호화 대상 필드:<code>party_info</code>,<code>party_info_hash</code>
        - 레코드당 예상크키는 약 <strong>4-5 KB</strong>입니다.
      </td>
      <td>일일 백업 권장</td>
    </tr>
  </tbody>
</table>
`}</HTMLBlock>

### TravelRule/VerifyName 공통 필수 테이블 생성 쿼리

TravelRule과 VerifyName을 모두 구현하는 VASP의 경우 두 프로토콜에 대해 공통으로 사용하는 필수 테이블들을 생성하기 위한 쿼리입니다. VerifyName을 연동하는 과정에서 아래 테이블들이 이미 생성 되었다면 이 단계를 건너뛸 수 있습니다.

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

### VerifyName 필수 테이블 생성 쿼리

VerifyName 프로토콜에서만 필수로 사용되는 테이블을 생성하기 위한 쿼리입니다. Enclave를 VN2 모드로 구동하는 경우 아래 쿼리를 반드시 실행하여 필수 테이블을 생성하고 키를 구성하십시오.

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
    `results` varchar(4000) DEFAULT NULL,
    `result` varchar(128) DEFAULT NULL COMMENT 'Final Verification Result',
    `status` varchar(40) NOT NULL,
    `reason` varchar(256) DEFAULT NULL,
    `message` varchar(1024) DEFAULT NULL,
    `version` varchar(16) DEFAULT NULL,
    `verified_at` datetime(3) DEFAULT NULL,
    `ordered_at` datetime(3) DEFAULT NULL,
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
      results varchar(4000) DEFAULT NULL,
      result varchar(128) DEFAULT NULL,
      status varchar(40) NOT NULL,
      reason varchar(256) DEFAULT NULL,
      message varchar(1024) DEFAULT NULL,
      version varchar(16) DEFAULT NULL,
      verified_at timestamp DEFAULT NULL,
      ordered_at timestamp DEFAULT NULL,
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
    results nvarchar(4000) DEFAULT NULL,
    result nvarchar(128) DEFAULT NULL,
    status nvarchar(40) NOT NULL,
    reason nvarchar(256) DEFAULT NULL,
    message nvarchar(1024) DEFAULT NULL,
    version nvarchar(16) DEFAULT NULL,
    verified_at datetime2(3) DEFAULT NULL,
    ordered_at datetime2(3) DEFAULT NULL,
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
    "results" varchar2(4000) DEFAULT NULL,
    "result" varchar2(128) DEFAULT NULL,
    "status" varchar2(40) NOT NULL,
    "reason" varchar2(256) DEFAULT NULL,
    "message" varchar2(1024) DEFAULT NULL,
    "version" varchar2(16) DEFAULT NULL,
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
  </Tab>
</Tabs>

### TravelRule 선택 테이블 생성 쿼리: Chainalysis Sanction 스크리닝

Chainalysis의 Sanction API를 사용하여 스크리닝 기능을 활성화하는 경우 반드시 아래 쿼리를 실행하여 관련 테이블을 생성하십시오.

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

### TravelRule 선택 테이블 생성 쿼리: Chainalysis KYT 스크리닝 사용시

Chainalysis의 KYT 사용하여 스크리닝 기능을 활성화하는 경우 반드시 아래 쿼리를 실행하여 관련 테이블을 생성하십시오

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

### TravelRule 선택 테이블 생성 쿼리: Refinitiv WCO 스크리닝 사용시

Refinitiv의 WCO API를 사용하여 스크리닝 기능을 활성화하는 경우 반드시 아래 쿼리를 실행하여 관련 테이블을 생성하십시오.

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