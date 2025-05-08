---
title: Understanding Travel Rule
deprecated: false
hidden: false
metadata:
  robots: index
---
## Introduction

* The FATF (Financial Action Task Force) is an international body that was established in 1989 by the G7 to combat money laundering globally. It sets international anti-money laundering (AML) and counter-terrorist financing (CTF) standards through recommendations, which over 200 jurisdictions have committed to implementing. Failure to align with FATF standards can result in countries being placed on grey or blacklists.
* In 2016, the FATF recognized Virtual Assets (VAs) and their growing role in the global financial system including their potential use in illicit activities. This marked the beginning of a structured approach to regulating virtual assets and their service providers within the existing AML/CTF framework.
* The FATF released its first recommendations for Virtual Asset Service Providers (VASPs) in 2019, establishing clear guidelines for how countries should develop regulations. The rationale for defining and regulating VASPs and the transfers they make was that sooner or later, illicit actors have to rely on these types of service providers to move assets or access fiat rails.
* These recommendations were updated in October 2021 with the release of the [Updated Guidance for a Risk-Based Approach to Virtual Assets and Virtual Asset Service Providers](https://www.fatf-gafi.org/en/publications/Fatfrecommendations/Guidance-rba-virtual-assets-2021.html#:~:text=In%20October%202021%2C%20the%20FATF,virtual%20assets%20and%20VASP%20sector) , which many jurisdictional regulations are based on. Recommendation 16 (R.16), known as the Travel Rule, was detailed further as a key pillar in combating illicit financial flows in the virtual asset space. It requires VASPs to collect and transmit specific information about the originator and beneficiary for virtual asset transfers, similar to traditional wire transfers. This includes names, account numbers, physical addresses, and national identity numbers, immediately and securely.
* Additionally, R.16 prescribes several key requisites such as counterparty due diligence, data protection and functionality of technological solutions.
* Given the focus on virtual assets, the FATF publishes key [updates](https://www.fatf-gafi.org/en/publications/Fatfrecommendations/targeted-update-virtual-assets-vasps-2024.html)  addressing the challenges of implementing the Travel Rule across different jurisdictions, providing practical examples and best practices for compliance. The [2023 Update](https://www.fatf-gafi.org/en/publications/Fatfrecommendations/targeted-update-virtual-assets-vasps-2023.html)  featured questions for travel rule tool providers, which VerifyVASP responded to and had audited. The updates also included strengthened requirements for VASP licensing and registration.

<br />

## The Creation of a Regulated Ecosystem

FATF R.15 (requiring licensing of VASPs) and R.16 (Travel Rule) are critical policy tools in the creation of a regulated ecosystem. Access to banking fiat rails is increasingly being determined by the robustness of the AML/CTF measures taken by a VASP. Whilst regulated, travel rule-obliged VASPs will have the legal basis to meet the FATF requirements, unregulated VASPs will need to level up to maintain or regain access to fiat rails and agree to enhanced risk mitigation measures if they want to be able to continue transacting with regulated VASPs.

Step by Step Guidance for Travel Rule Implementation

### Step 1: Identify Key Counterparties

The FATF suggests a 3-phase process to determine whether a transaction is with a counterparty VASP or a different entity such as an unhosted wallet.

<Image align="center" src="https://files.readme.io/a7768410f90e26b07c7a373c340f95b8bf03fc4c3aa7100446f5b12b75d3e7f2-welcome_1.avif" />

Determination is followed by identification and assessment of the counterparty VASP. The requirement is to be conducted at the legal entity level. For example, if a VASP has affiliated legal entities in multiple jurisdictions, the counterparty VASP is deemed to be the legal entity in which the account holder or user (the beneficiary or originator to that transaction) is onboarded and AML/CFT risk mitigation performed.

### Step 2: Conduct Counterparty Due Diligence

Following identification and assessment, a VASP must conduct due diligence (“DD”) on all of its counterparties, before entering into a new business relationship and on a regular basis thereafter. The purpose of the counterparty DD is to prevent dealing with illicit or sanctioned actors and ensure the counterparty can reasonably be expected to be able to protect the received personal information.

Any elevated AML/CFT risks identified according to FATF standards should be appropriately mitigated and the FATF suggests adapting the Wolfsberg questionnaire on correspondent banking risk as a framework to conduct counterparty DD on VASPs. VerifyVASP has a proprietary adaptation of the Wolfsberg DDQ.

### Step 3: Establish a Legal Basis to Transmit Personal Data

Prior to Travel Rule implementation, VASPs need to ensure legal basis:

* Sufficient disclosure to users
* Specific user consent on each Travel Rule data sharing
* Establishing legal basis to transmit personal data to each counterparty VASP
* Establishing legal basis to transmit personal data to the Travel Rule solution provider

During the course of the counterparty DD, a VASP must:

* Assess if a certain counterparty has sufficient capability and the legal obligation to securely handle personal data.
* Understand personal data-related restrictions of the counterparty.

If a certain counterparty is not obligated to comply with Travel Rule requirements, it may lack the necessary legal basis to share Travel Rule information. In such a case, an enhanced risk mitigation measure such as VerifyName should be used.

In addition to establishing the legal basis, the local regulation may also require the VASP to enter into a data processing agreement with the Travel Rule solution provider in order to define the respective responsibilities of the VASP as data controller and the Travel Rule solution provider as data processor. VerifyVASP has templates for this.

### Step 4: Understand the User Experience and Communicate the Changes

Travel Rule is a relatively new initiative and may be perceived as a hassle to the user. In VerifyVASP’s solution, the ordering VASP needs to:

* Ask its users for the names of the beneficiary and beneficiary VASPs.
* Define a list of approved VASPs for the user to choose from.
* Communicate back to the user any beneficiary verification fails.

This can result in a VASP needing to prepare additional UX for virtual asset deposits and withdrawals.

### Step 5: Collect Counterparty Data from User and Verify the Accuracy

A common mistake in Travel Rule implementation occurs when an ordering VASP focuses more on the submission of the required information than verifying the accuracy of declared information (beneficiary VASP and beneficiary), resulting in ineffective name screening on both sides. In such cases, the Travel Rule information can be transmitted to the wrong party, resulting in non-compliance with Travel Rule and breach of the personal data protection regulations.

In cases where the beneficiary information is not consistent, the beneficiary VASPs may not process the transfer in and may choose to return the virtual asset back, resulting in unnecessary user complaints to the ordering VASP. For these reasons, we are of the opinion that the verification of beneficiary information should be made compulsory, in practice.

<Image align="center" src="https://files.readme.io/132e5a82e5966bb2d0222b3fd543f41d4f264a1985ddf6b902646ce070a21ec2-welcome_2.png" />

### Step 6: Understand the Implications of “immediately” and “securely”

The FATF guidance and most local regulations require the Travel Rule information to be submitted immediately and securely.

“Immediately” means that the ordering VASP should submit required information prior to, or simultaneously or concurrently with, the virtual asset transfer (i.e. the submission must occur before or at the same time as the virtual asset transfer is conducted, but not after). For example, transmitting Travel Rule information after the transfer of the virtual asset (for the purpose of obtaining TXID) is not deemed as ‘immediate’.

“Securely” means that the ordering institution should store and submit the required information in a secure manner to protect the integrity and availability of the required information and protect the information from unauthorised access or disclosure. Several measures are suggested or imposed to ensure security:

* Undertake counterparty due diligence to determine whether the beneficiary institution and, where applicable, the intermediary institution can reasonably be expected to adequately and are legally obliged to protect the confidentiality and integrity of the information submitted to it.
* Bilateral data sharing agreements with the beneficiary institution and, where applicable, the intermediary institution and/or a service-level agreement with the technological solution provider for travel rule compliance, using, or ensuring the technological solution adopted for travel rule compliance uses a strong encryption algorithm to protect the data.

### Step 7: Develop an Effective Enhanced Risk Mitigation Measure

If a counterparty is unregulated or otherwise Travel Rule non-obliged, it may not have a legal basis to transfer personal data. In such cases, the FATF guidance suggests obliged VASPs apply a risk-based approach and implement enhanced risk mitigation measures.

A common enhanced risk mitigation measure is to limit transactions to/from first party transfers only. In such a case, both the originator and the beneficiary are the same user of the VASP. The VASP can in such an instance, rely on its own due diligence on its user including onboarding screening and on-going monitoring. VerifyVASP’s VerifyName is an effective enhanced risk mitigation measure.

### Step 8: Develop a Policy for Travel Rule Non-Compliant Deposits

Upon the implementation of the Travel Rule, there will inevitably be Travel Rule non-compliant deposits.

In practice, there will be 4 types of Travel Rule non-compliant deposits:

* Deposits originating outside of approved VASPs
* Deposits from approved VASP with insufficient Travel Rule information
* Deposits from approved VASP with inconsistent beneficiary information
* Deposits from approved VASP where originator is not an allowed person (in case of first party transfer limitation)

A VASP needs to prepare policy and workflow to address each case well in advance. Communication with users on the policy is essential to minimize user disruption.

A common practice adopted by regulated VASPs is to freeze the assets until Travel Rule compliance has been achieved and ultimately return if it is not achieved.

### Step 9: Develop a Detailed Workflow for the Return Process

The FATF guidance or local regulations does not specifically prescribe requirements on Travel Rule non-compliance return policy. This leads to VASPs adopting various practices on return policies. Key considerations on a return policy are; i) where to return to, ii) who to return to, iii) applicability of Travel Rule compliance.

<Image align="center" src="https://files.readme.io/c334ed5f9ce58b0950b3770f6968e05f97ff07a69ba9adf142b07447959a3596-welcome_3.avif" />

In case the originator is not the same person as the intended beneficiary, there is a complication of who to return the assets to: back to the originator or to a wallet address in the name of the intended beneficiary managed in another VASP (among the approved VASPs in the context of counterparty DD).

### Step 10: Develop an Enhanced Risk Mitigation Measure for Unhosted Wallets

Whilst FATF specifies that unhosted wallets are out of scope for the Travel Rule, the FATF guidance and subsequent updates highlight their inherent risks and suggest a variety of mitigation measures. Under this guidance, most of the local regulations mandate certain risk mitigation measures towards unhosted wallets. Some jurisdictions are restricting transfers to or from unhosted wallets to first party transfers only, requiring ownership verification. Below are verification practices of varying reliability.

<Image align="center" src="https://files.readme.io/726acd5bf8947526182933218e088260d392d7ea673b4031a198f73c2d930358-welcome_4.png" />