---
title: TravelRule VS VerifyName
deprecated: false
hidden: true
metadata:
  robots: index
---
## Differences in Functionalities

**TravelRule** is designed to ensure comprehensive compliance for regulated VASPs, accommodating complex asset transfers that involve different individuals as the originator and beneficiary. This solution supports full fourth-party verification, where the entire chain—from the originator to the originating VASP, the beneficiary VASP, and the beneficiary—is validated to ensure regulatory standards are met. TravelRule also incorporates advanced features, such as multi-party verifications and integration with third-party screening services, making it ideal for jurisdictions with stringent AML and CFT regulations.

In contrast, **VerifyName** is tailored for non-obliged VASPs, offering a streamlined approach to compliance. It focuses solely on third-party relationships, allowing asset transfers only when the originator and beneficiary are the same individual. VerifyName simplifies the verification process while maintaining essential safeguards, providing a practical solution for VASPs operating in jurisdictions with less rigorous compliance mandates.

<br />

## Travel Rule Obliged VASP vs. Non-Travel Rule Obliged VASP

The determination depends on regulatory requirements and the operational scope of your organization. Because regulatory frameworks vary widely by jurisdiction, some VASPs may qualify as Travel Rule Non-Obliged, requiring only basic identity checks (VerifyName), while others fall under the Travel Rule Obliged category, mandating comprehensive due diligence (TravelRule). For example, jurisdictions with stricter AML regulations often require a more detailed verification process. Review the compliance guidelines specific to your jurisdiction, and consult your legal or compliance teams to clarify the exact requirements.

Typically, smaller or regionally restricted VASPs may fall into the Travel Rule Non-Obliged category, needing only VerifyName implementation, which focuses on basic identity checks without additional compliance measures. In contrast, Travel Rule Obliged VASPs are subject to broader regulatory requirements, such as identity verification, financial history checks, Anti-Money Laundering (AML), and Know Your Customer (KYC) compliance. This classification is more common for larger or globally regulated VASPs.

**A Travel Rule Obliged VASP** complies with licensing or registration requirements set by its local jurisdiction and operates under strict regulatory frameworks, including AML and CFT measures. These VASPs adhere to international standards, such as the Financial Action Task Force (FATF) Travel Rule, which mandates the secure collection and transmission of originator and beneficiary information during virtual asset transactions. Travel Rule Obliged VASPs undergo regular regulatory oversight, covering areas such as IT security, personal data protection, and transaction monitoring. If your organization is licensed or registered with a recognized regulatory authority and follows these compliance standards, you are considered a Travel Rule Obliged VASP and should use the TravelRule Protocol for transactions.

**A Travel Rule Non-Obliged VASP**, on the other hand, operates without meeting local licensing or registration requirements. Such entities are not obligated to comply with AML/CFT regulations or FATF Travel Rule guidelines, making them more vulnerable to risks like money laundering or terrorism financing. Consequently, Travel Rule Obliged VASPs are often required to conduct enhanced due diligence or apply stricter risk mitigation measures when interacting with Travel Rule Non-Obliged VASPs. If your organization is neither licensed, registered, nor compliant with FATF standards, you fall into the Travel Rule Non-Obliged VASP category and should adopt the VerifyName Protocol, which is tailored to enable safe and limited interactions with Travel Rule Obliged VASPs.

> 📘 Note:
>
> Avoid making this decision based solely on technical assumptions or project expectations. Properly assessing this requirement in collaboration with compliance and legal teams is critical to ensure adherence to regulatory standards and avoid potential penalties.

<br />

## Identify your scope for integration

<Image align="center" border={false} caption="Diagram 1. Protocol among Travel Rule obliged VASPs and non-obliged VASPs" src="https://files.readme.io/060b94af81c9aa3dcfa8c7b767af2789fe1fd743c59c79d2a58a21e3394925c8-travelrule_vs_verifyName.png" />

**For Travel Rule Obliged VASPs**, the integration scope includes two key components: implementing **the TravelRule Protocol** to support verification workflows with other Travel Rule Obliged VASPs and integrating with the Enclave to enable verification request flows using the **VerifyName Protocol** for interactions with Non-Travel Rule Obliged VASPs. These steps ensure comprehensive compliance across both regulated and unregulated VASPs. For detailed implementation steps, refer to the [TravelRule Integration Guide](ref:travelrule-api-implementation)  and the [VerifyName Integration Guide for Obliged VASPs](ref:verifyname-api-implementation).

**For Non-Travel Rule Obliged VASPs**, the integration scope is minimal. To support asset transfers to and from Travel Rule Obliged VASPs, Non-Obliged VASPs only need to implement the **VerifyName API**. This API allows safe, limited verification flows while requiring no pre-verification workflows or extensive compliance measures. To begin integration, consult the [VerifyName Integration Guide for Non-Obliged VASPs]() . (문장 변경 후, 링크 유지 의사결정 필요)