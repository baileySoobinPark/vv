---
title: Risk Assessment Via Chainalysis Sanction API
api:
  file: TR_Enclave_API_Spec.yaml
  operationId: travelrule-Chainalysis-Sanction
hidden: false
---
This API uses the Chainalysis Sanction API to assess the risk level of a wallet address in advance. Based on the response, a VASP can identify potentially high-risk or unauthorized addresses and prevent related transactions proactively.

***

## What is Chainalysis Sanction API?

Provided for free by [Chainalysis](https://www.chainalysis.com/), this API is used to check whether a crypto wallet address is sanctioned or high-risk. It enables institutions, including VASPs, to quickly identify addresses flagged under regulatory or sanctions watchlists. The API helps fulfill AML and sanction screening requirements more efficiently, enhancing the safety and trustworthiness of virtual asset transfers.

* To use the API, obtain an API key via the Chainalysis [Sign-Up Page](https://go.chainalysis.com/crypto-sanctions-screening.html).
* For usage instructions and technical details, refer to the [official docs](https://public.chainalysis.com/docs/index.html).

## Integration Guide

Both ordering and beneficiary VASPs may use the Sanction API to perform additional risk assessments on user accounts, which may assist in establishing internal compliance rules. The Sanction API must only be called after the user verification API (POST /verifications) has been invoked.

* Ordering VASP: Can assess the risk of the beneficiary's wallet address after verification and decide whether to proceed with the asset transfer based on internal policies.
* Beneficiary VASP: Can evaluate the ordering wallet address to detect potential threats and take preventive measures if needed.

<br />

## Pre-Implementation Checklist

1. **Enclave 환경 변수 설정**\
   Enclave 구동 전, 등록 과정에서 발급받은 API 키를 `VEGA_CHAINALYSIS_SANCTION_API_KEY` 환경 변수에 반드시 설정해야 합니다.
2. **데이터베이스 테이블 구성**\
   Enclave 서버가 Chainalysis Sanction API 결과를 저장할 수 있도록 데이터베이스 내에 전용 테이블을 구성해야 합니다. 구현 세부사항은 [Enclave 데이터베이스 생성](ref:database-setup-copy) 페이지의 Chainalysis 관련 테이블 정의를 참조하세요.

<br />

1. **Set Enclave Environment Variable**\
   Before running the Enclave, make sure to set the API key issued during registration in the `VEGA_CHAINALYSIS_SANCTION_API_KEY` environment variable.
2. **Configure Database Tables**\
   Ensure the Enclave server can store Chainalysis Sanction API results by creating dedicated tables in your database. Refer to the Chainalysis table definitions in the [Enclave Database Setup](ref:database-setup-copy) documentation for implementation details.

<br />

## API Specification