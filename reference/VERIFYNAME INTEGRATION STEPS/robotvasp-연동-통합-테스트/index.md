---
title: RobotVASP 연동 통합 테스트
excerpt: >-
  주요 연동 작업이 완료되면 VerifyVASP가 제공하는 Robot VASP를 상대 VASP로 하여 모의 입출금을 테스트해볼 수 있습니다.
  본 문서에서는 모의 테스트를 통해 주요 케이스에 대한 정상 연동 여부를 확인하는 방법을 안내합니다.
deprecated: false
hidden: false
metadata:
  robots: index
---
## What is Robot VASP?

The Robot VASP is a virtual VASP designed to conduct deposit and withdrawal tests with your VASP. By interacting with Robot VASP, your VASP can verify the functionality of its implemented Enclave server and VASP APIs.

In this test, your VASP can conduct tests listed below.

Verifying information of natural person who has already completed KYC with the Robot VASP.\
Verifying information of natural person who has not complete KYC with with the Robot VASP.
Verifying information of legal person who has already completed KYC with the Robot VASP.
AS the results of each test case, you can also conduct tests on Report Transaction or Report Error. Your VASP can experience the whole process of the VerifyVASP solution and test the functionality of your VASP API and Enclave server by conducting the Robot VASP test.