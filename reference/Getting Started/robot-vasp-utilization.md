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