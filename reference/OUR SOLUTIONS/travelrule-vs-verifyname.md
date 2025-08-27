---
title: TravelRule VS VerifyName
excerpt: >-
  VerifyVASP provides two core protocols — TravelRule and VerifyName — each
  designed to address different VASP compliance requirements. Select and
  implement the protocol that best meets your VASP’s Travel Rule obligations.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Key Differences

**TravelRule**

* Used between VASPs that are subject to Travel Rule obligations.
* Enables the secure exchange and mutual verification of originator and beneficiary personal information during virtual asset transfers.
* Supports transfers whether the originator and beneficiary are the same person or different individuals.
* Supports **pre-verification only**.

<br />

**VerifyName**

* Used between a Travel Rule–obliged VASP and a non-obliged VASP.
* Allows the obliged VASP to perform **one-way verification** to meet compliance requirements.
* Transfers are permitted **only when the originator and beneficiary are the same person**.
* Instead of exchanging personal data directly, the protocol compares hashed values of the name and date of birth to confirm identity.
* Supports both **pre-verification** and **post-verification**.

<br />

<Image align="center" src="https://files.readme.io/060b94af81c9aa3dcfa8c7b767af2789fe1fd743c59c79d2a58a21e3394925c8-travelrule_vs_verifyName.png" />

## Implementation Requirements and Recommendations

* **Travel Rule–obligated VASPs**: Must implement the **TravelRule protocol**. Implementation of VerifyName is optional but recommended. Supporting both protocols allows transfers to and from non-obligated VASPs, expanding transaction coverage.
* **Non-obligated VASPs**: Can implement **VerifyName** to support transfers with Travel Rule–obligated VASPs.

<br />

## Determining Your VASP's Obligation

Your VASP’s Travel Rule obligations depend on the regulatory requirements in your jurisdiction. Consult with experts in legal or compliance matters to determine:

* Whether VASP licensing, registration, or AML/CFT compliance is required in your jurisdiction.
* Which protocol — **TravelRule or VerifyName** — is appropriate for your operations.
