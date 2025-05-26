---
title: (KR)-Overview
deprecated: false
hidden: true
metadata:
  robots: index
---
## Architecture

<Image align="center" border={false} caption="Diagram 1. VerifyVASP Integration Architecture Overview" src="https://files.readme.io/8d27021ec8f83d7f4cc31b17bccc04e96360c65217d142e4733739024c89930b-tr_solution_1.png" />

### Travel Rule Commination

VASP는 VerifyVASP의 중앙 서버(Central Server)를 통해 상대 VASP와 요청 및 응답을 주고받으며, 입.출금 상황에 따라 송신 VASP 또는 수신 VASP의 역할을 번갈아 수행합니다.

### Enclave 설치 및 연동

Enclave는 VerifyVASP에서 제공하는 사전 구축된 서버로, VerifyVASP Central Server와 연동할 수 있도록 설계된 프로토콜 인터페이스를 제공합니다. 이 서버는 VASP의 자체 인프라 내에 설치되어야 하며, Enclave API를 통하여 상대 VASP와 검증 요청을 주고 받을 수 있습니다.

### 데이터 보안 및 개인정보 보호

Enclave 데이터베이스는 VASP 만 접근할 수 있도록 제한되어 있어, 데이터 무결성과 격리가 보장됩니다. 또한, VASP 간 및 중앙 서버와의 통신은 HTTPS 기반의 암호화된 채널을 통해 다중 보안 계층을 적용하여 수행됩니다. 이를 통해, 검증 및 거래 과정에서 오가는 개인정보 등 민감한 데이터가 안전하게 보호됩니다.

<br />

## 검증 흐름 (High-level Verification Flow)

1. **송금인의 자산 전송 요청**

   송신인이 자산 전송 요청을 시작하면, 송신 VASP는 송신인 정보와 송신인으로부터 수집한 수신인 정보를 취합합니다.
2. **검증 요청**

   송신 VASP는 수집된 정보를 Enclave 서버를 통해 VerifyVASP 중앙 서버로 전송하며, 해당 정보를 수신 VASP에게 전달합니다.

   * 이때 전달되는 송.수신인 개인정보는 양 VASP의 Enclave 에서 종단간 암복호화 됩니다.
   * Enclave는 VerifyVASP에서 제공하는 Docker 이미지 형태로 배포되며, 전용 데이터베이스 구성이 필요합니다.
   * VASP의 백엔드는 Enclave 서버와만 통신하며, VerifyVASP Central Server API와 직접 통신하지 않습니다.
3. **검증 수행**

   수신 VASP는 검증 요청을 수신한 후, 자체 보유한 기록을 바탕으로 수신인 정보를 확인 후 검증합니다.

   * 송신 VASP 로부터 전달받은 송신인 정보는 별도 검증을 하지 않습니다.
4. **검증 결과 전달**

   After verification, the Beneficiary VASP sends the result back to the Originating VASP via VerifyVASP central server. This process includes both synchronous and asynchronous API interactions, with specific details provided in the subsequent document on scenarios and flows.
5. **Transaction Completion**

   Based on the verification result, if approved, the Originating VASP completes the withdrawal transaction on the blockchain and shares the transaction outcome with the Beneficiary VASP to confirm the transfer. The Beneficiary VASP may also query the transaction status as needed.

<br />

This process establishes a secure and structured flow of requests and responses between VASPs, ensuring efficient identity verification and transaction handling.

<br />

## Secured Data Exchange

### End-to-End Encryption for VASP Data Exchange

VerifyVASP TravelRule ensures the privacy of personal information shared during user verification by implementing robust end-to-end encryption (E2EE). This encryption guarantees that only the Originating and Beneficiary VASPs can decrypt the shared data. The VerifyVASP central server acts solely as a mediator and neither decrypts nor stores personal information.

The process of E2EE is managed seamlessly by the enclave servers of the involved VASPs, as outlined below:

1. **Initiating a Verification Request**
   * The Originating VASP's backend sends a beneficiary verification request via its enclave server's API.
2. **Public Key Retrieval**
   * The Originating VASP's enclave server requests the Beneficiary VASP's public key through the VerifyVASP central server.
   * The Beneficiary VASP's enclave server retrieves the public key from its database. If no key exists, the server generates, saves, and returns a new public key.
3. **Encrypting Personal Information**
   * Upon receiving the Beneficiary VASP's public key, the Originating VASP's enclave server encrypts the user's personal information.
   * The encrypted data is sent to the Beneficiary VASP's enclave server via the VerifyVASP central server.
4. **Decryption and Verification by Beneficiary VASP**
   * The Beneficiary VASP's enclave server decrypts the data using its private key.
   * It verifies the user information through its VASP API.
   * Next, it encrypts the Beneficiary's personal information using the Originating VASP’s public key, which is included with the encrypted data.
5. **Returning Encrypted Data**
   * The encrypted information is sent back to the Originating VASP's enclave server through the VerifyVASP central server.
6. **Final Decryption and Storage**
   * The Originating VASP's enclave server decrypts the data using its private key.
   * The decrypted information is securely stored in the enclave database.

### Key Management

The public and private keys used for encryption and decryption are automatically generated, saved, and managed within the enclave server database. This automation ensures that VASP backend systems do not need to handle E2EE directly, simplifying the implementation.

### Additional Options

While the enclave server fully manages the encryption process, optional configurations are available to enhance flexibility and control. Familiarity with E2EE concepts can further streamline and optimize this workflow.

This approach ensures secure and seamless data exchange while maintaining the privacy and integrity of sensitive user information.

<Accordion title="Configurable Key Options for End-to-End Encryption">
  ## Public Key Caching

  To improve the efficiency of public key retrieval, the enclave server caches the public key of the counterparty VASP for a configurable duration. This reduces the need for repeated requests to fetch the same key.

  * **Enclave Environment Variable**
    * Configure the caching duration in milliseconds using the variable: `VEGA_PUBLIC_KEY_TTL`.
  * **Default and Minimum Values**
    * **Default**: **`1800000`** milliseconds (30 minutes).
    * **Minimum**: **`600000`** milliseconds (10 minutes).
      Adjust this setting to balance performance and security based on your requirements.

  ## Public Key Types

  The enclave server supports multiple public key types to provide flexibility and enhanced security for end-to-end encryption during the verification process. The key type can be specified in the request body when calling the verification API. Supported keyTypes are as below.

  * `PerVasp`
    * A single key shared across all verifications for a VASP.
      * **`Pros`**: Most efficient option due to caching.
      * **`Cons`**: Least secure, as the same key is used for all verifications.
  * `PerAddress`
  * A unique key is generated for each beneficiary address.
    * **`Pros`**: More secure than PerVasp, as each address is encrypted with a unique key.
    * **`Cons`**: Slightly less efficient compared to PerVasp.
  * `PerVerification`
  * A new key is generated for every verification request.
    * **`Pros`**: Most secure, as each request uses a unique key.
    * **`Cons`**: Least efficient, as caching is not applicable.
      By selecting the appropriate caching duration and key type, you can tailor the balance between security and efficiency to meet your organization’s needs.
</Accordion>

<br />

## VASP Side Action Items for TravelRule Integration

To successfully implement TravelRule, here are the key steps VASP developers need to follow. Each step links to detailed guidance provided in subsequent sections.

1. **Review Key Scenarios and Flows**

   Gain a thorough understanding of the essential use cases and operational flows before beginning the implementation process.
2. **Define Project Scope with Future-State VASP Architecture**

   Align your implementation plan with the TravelRule architecture to ensure compatibility and scalability.
3. **Complete Onboarding**

   Register and authenticate your VASP with VerifyVASP to obtain the credentials required for integration.
4. **Develop VASP APIs**

   Implement the APIs necessary to manage transaction and verification workflows effectively.
5. **Configure a Enclave Database**

   Set up a database to store and manage verification and compliance data securely.
6. **Deploy the Enclave**

   Install and configure the Enclave Docker environment to securely connect to the VerifyVASP Central Server.
7. **Test with Robot VASP**

   Use the Robot VASP for end-to-end testing to validate your implementation and ensure protocol compliance.
8. **Plan for Ongoing Maintenance**

   Develop a strategy for maintaining the integration to accommodate regulatory updates and TravelRule enhancements.

<br />

By following these steps, you’ll be well-equipped to establish a secure and compliant integration with VerifyVASP.