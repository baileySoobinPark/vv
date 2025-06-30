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

DBMS 설치가 완료되면, Enclave가 사용할 테이블들을 생성해야 합니다. 전체 스키마는 필수 테이블 4개(TravelRule 공통 사용 필수 테이블 3개)로 구성됩니다. 각 테이블에 대한 상세 설명은 아래 표와 같습니다.

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
      <td>백업 또는 복원 정책<br>불필요</td>
    </tr>
    <tr>
      <td class="code-col"><code>commands</code></td>
      <td>
        - <b>필수 테이블입니다.</b> <br>
        - Enclave 내부적으로 비동기 API의 중간 처리 상태를 저장합니다.<br> 
        - 레코드당 예상크키는 약 <strong>1-5 KB</strong>입니다.
      </td>
      <td>백업 또는 복원 정책<br>불필요</td>
    </tr>
		<tr>
      <td class="code-col"><code>owner_verifications</code></td>
      <td>
        - <b>필수 테이블입니다.</b> <br>
        - 계좌 소유주 검증 요청 및 결과 이력을 저장합니다.<br> 
 				- POST /v2/owner-verifications API 호출시 요청과 응답 데이터가 저장됩니다.<br>
        - 암호화 대상 필드:<code>party_info</code>,<code>party_info_hash</code><br>
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