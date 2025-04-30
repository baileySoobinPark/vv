---
title: Withdrawal
excerpt: >-
  This guide provides the withdrawal test scenario, test cases and expected
  results for the withdrawal test.
deprecated: false
hidden: true
metadata:
  robots: index
---
> 💡 Please Note:
>
> The withdrawal test is supported only in the Ethereum Sepolia and Ripple Testnet environments.
>
> Please double-check the environment before executing the virtual asset transfer transaction.

<br />

## Withdrawal Test Scenario

1. Identifying the Robot VASP to interact with your VASP

2. The Robot VASP verifies the Beneficiary's **account information** provided by your VASP.

3. The Robot VASP verifies the Beneficiary's **personal information** provided by your VASP.

4-1. Your VASP executes a virtual asset transfer transaction on the blockchain network.

Case 1: After executing the transaction, your VASP **sends the transaction hash** or transaction ID to the VV Central Server.

Case 2: After executing the transaction, your VASP **does not send the transaction hash** or transaction ID to the VV Central Server.

4-2. **Cancel the transaction that has completed user verification.**

Case 1: For a halted virtual asset transfer, your VASP **sends an Error Report** to the VV Central Server.

Case 2: For a halted virtual asset transfer, your VASP **does not send an Error Report** to the VV Central Server.

<br />

## Test User Informations of RobotVASP

Here are virtual user informations that stored on Robot VASP for conducting test. Your VASP can conduct each case of withdrawal test using this information.

<Accordion title="User 1(Individual user who has completed KYC verification)">
  If all the information is entered correctly, the verification result must return `VERIFIED`.

  ### Individual Information

  * **Last Name**: Robbins

  * **First Name**: Taylor

  ### Wallet Address

  **'ETH' address**: `0xFa230E9cCAF5e382539147294d7965Eeccbbfa5c`

  **'XRP' address**: `rGFFufDwabHuPur9927p1EgBTcCBfsjtEU`

  **'XRP' destination tag**: `123456789`
</Accordion>

<Accordion title="User 2(Individual user who has not completed KYC verification)">
  Even if all information is entered correctly, the verification result must return `DENIED`.

  ### Individual Information

  * **Last Name**: Cook

  * **First Name**: Ethan

  ### Wallet Address

  **'ETH' address**: `0x319E92715729c46869ed31d228f3b4f31e951450`

  **'XRP' address**: `rGFFufDwabHuPur9927p1EgBTcCBfsjtEU`

  **'XRP' destination tag**: `345678912`
</Accordion>

<Accordion title="User 3(Corporate user who has completed KYC verification)">
  If all the information is entered correctly, including the representative's name, the verification result must return `VERIFIED`.
  If the representative's name is missing or incorrect, the verification result should return `DENIED`, even if all other information is entered correctly.

  ### Individual Information

  * **Corporate Name**: Garrit Studio

    ### Representative Name

  * **Last Name**: Clarke

  * **First Name**: Jason

  ### Wallet Address

  **'ETH' address**: `0x26704Dc20d0ddF6cAa45b4D2b8AcB643015B951E`

  **'XRP' address**: `rGFFufDwabHuPur9927p1EgBTcCBfsjtEU`

  **'XRP' destination tag**: `234567891`
</Accordion>