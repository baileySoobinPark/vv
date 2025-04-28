---
title: Robot VASP Utilization
deprecated: false
hidden: true
metadata:
  robots: index
---
You can use Robot VASP to simulate originator / beneficiary verification and deposit / withdrawal process through VerifyVASP.

* You can check whether the implementation related to travel rules works as intended in the withdrawal scenario by testing with Robot VASP as Beneficiary VASP.
* You can check whether the implementation related to travel rules works as intended in the deposit scenario by testing with Robot VASP as Originating VASP.

<br />

## User Information on Robot VASP

The following user details are test users hosted on Robot VASP. These are designed to cover most of the test cases needed to verify the VerifyVASP travel rule API implementation on your environment. Use the following user details as originator or beneficiary details when simulating withdrawals or deposits with Robot VASP as your counterparty.

<Accordion title="User 1(KYC verified natural person" icon="fa-info-circle">
  This user can be assumed as a natural person who has already completed KYC verification. Therefore, if all information is entered correctly, you can receive VERIFIED verification results.

  * Personal Information

    * Last name : Robbins

    - First name : Taylor

  - Wallet Addresses

    * 'ETH' address: '0xFa230E9cCAF5e382539147294d7965Eeccbbfa5c'

    * 'XRP' address: 'rGFFufDwabHuPur9927p1EgBTcCBfsjtEU'

    * 'XRP' destination tag: '123456789'
</Accordion>

<Accordion title="User 2(KYC unverified natural person" icon="fa-info-circle">
  This user can be assumed as a natural person who has not completed KYC verification. Therefore, if all information is entered correctly, you can receive DENIED (UNVERIFIED-KYC) verification results.

  * Personal Information

    * Last name : Cook

    * First name : Ethan

  * Wallet Addresses

    * 'ETH' address: '0x319E92715729c46869ed31d228f3b4f31e951450'

    * 'XRP' address: 'rGFFufDwabHuPur9927p1EgBTcCBfsjtEU'

    * 'XRP' destination tag: '345678912'
</Accordion>

<Accordion title="User 3(KYC verified legal person">
  This user can be assumed as a legal person who has already completed KYC verification. Therefore, when requesting user verification, the representative name must also be entered to receive VERIFIED verification results.

  * Personal Information

    * Legal person name : Garrit Studio

    * Representative information

      * Last name : Clarke

      * First name : Jason

  * Wallet Addresses

    * 'ETH' address: '0x26704Dc20d0ddF6cAa45b4D2b8AcB643015B951E'

    * 'XRP' address: 'rGFFufDwabHuPur9927p1EgBTcCBfsjtEU'

    * 'XRP' destination tag: '234567891'
</Accordion>