---
title: To-Be Architecture
excerpt: >-
  This page outlines the implementation scope required for your VASP to
  integrate with TravelRule protocols. This future-state design ensures your
  VASP is well-prepared to handle regulatory requirements and seamlessly
  integrate with TravelRule protocols. As shown in Diagram 1, the future-state
  architecture of your VASP’s business backend incorporates enhancements to
  support account and user verification processes seamlessly.
deprecated: false
hidden: true
metadata:
  robots: index
---
<Image align="center" border={false} caption="Diagram 1. Future-State VASP: Implementation Scope" src="https://files.readme.io/50e08e8212d7e0a2aaf3ca0ec924c3311a70710be8ef6e0273ea8d700c2d6b52-tr_to_be_arct_1.png" width="400px" />

**Integrating Verification into Withdrawal Processes**

To function as an Originating VASP, your system must first integrate account and user verification processes into the existing withdrawal workflow. This implementation ensures compliance with best practices outlined in the **Scenarios and Flows** page. Refer to the **Enclave API Reference** for detailed requirements and guidance.

**Implementing New APIs**

In addition to enhancing existing workflows, your VASP must implement five new APIs to enable TravelRule integration. The dashed box in Diagram 1 outlines the scope of these API implementations, which are essential for executing compliance logic. For detailed specifications, refer to the **Implementing VASP APIs** section in the integration guide.

<Image align="center" border={false} caption="Diagram 2. Future-State VASP: Final Architecture" src="https://files.readme.io/00c107710ed90f7bd0430933f94e66c07db48f94e255b7db41651086fcda0413-tr_to_be_arct_2.webp" />

Diagram 2 illustrates the Final Architecture of Future-State of VASP with interaction within the entire system. The Enclave Server, installed alongside the VASP Business Backend within the same infrastructure, interacts with the VASP Business Backend through API calls. It also manages Travel Rule records and related data in a dedicated Enclave Database.

* **Grey arrows** represent the interactions required when acting as an Originating VASP.
* **Green arrows** represent the interactions necessary when functioning as a Beneficiary VASP.

This diagram provides a comprehensive overview of your VASP’s TO-BE architecture. Use it as a reference to plan your development scope and align your implementation with the expected workflows.