---
title: (KR)TravelRule VS VerifyName
deprecated: false
hidden: true
metadata:
  robots: index
---
## 두 프로토콜의 차이점

**TravelRule** 은 Travel Rule 규제 의무가 있는 VASP간 송.수신인의 개인정보를 암호화하여 안전하게 주고 받을 수 있도록 설계되었습니다. 송신인과 수신인이 같거나 다른 경우 모두 지원이 가능합니다.

반면, **VerifyName**은 Travel Rule 규제 의무가 있는 VASP와 의무가 없는 VASP 간의 가상자산 송·수신을 지원하기 위해 설계된 솔루션입니다. 해당 솔루션은 송.수신인이 동일한 경우에만 자산 이동을 허용하며, 개인정보를 직접 주고받는 대신, 개인정보를 Hash 처리한 후 해당 Hash 값의 일치 여부를 통해 동일인 여부를 확인합니다.

<br />

## Travel Rule 의무 VASP vs. 비의무 VASP

VASP가 Travel Rule 의무 대상인지 여부는 관할 지역의 규제 요건에 따라 달라집니다. 관할 지역의 규제상 라이센스 또는 신고/등록이 요구되는지, AML/CFT 의무사항 여부 등 법무 또는 컴플라이언스 팀과 함께 정확한 요구사항을 파악하여 TravelRule 과 VerifyName 중 어떤 솔루션을 사용할지를 판단하십시오.

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