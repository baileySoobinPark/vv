---
title: Integration
deprecated: false
hidden: false
metadata:
  robots: index
---
### What steps should I take if there’s an error during data exchange with a counterparty?

If an error occurs while verifying with a counterparty, you can investigate the issue through VerifyVASP. For further assistance, share detailed information with the VerifyVASP team via email([corporate@verifyvasp.com](mailto:corporate@verifyvasp.com))or through the designated Slack channel.

### Why is the VASP List not showing up when calling the VASP List API after integration?

The VASP List will only appear once integration with counterparty member VASPs is complete. Until then, the list will not be available. For guidance or assistance, reach out to us via email ([corporate@verifyvasp.com](mailto:corporate@verifyvasp.com)) or the designated Slack channel.

### Is IP allowlisting required between the Central Server and the Enclave Server?

Yes, IP allowlisting is mandatory.

VerifyVASP enforces strict security policies, ensuring that data exchange occurs only between allowlisted member servers. This measure secures the transmission of sensitive Travel Rule information.

### Should we always allow VerifyVASP's external IP?

We strongly recommend allowing it. The VerifyVASP Central Server performs regular health checks on all member Enclave Servers. If a temporary issue arises with your Enclave, the VerifyVASP team can quickly detect it and notify you in real time.

### I'm a VASP supporting the TravelRule solution. Do I need updating the Enclave server for supporting the VerifyName?

No, you don’t. If your VASP has been supporting the TravelRule solution after 2022, then your Enclave server can support the VerifyName Enclave APIs. For this, your VASP only needs to implement the part for calling the VerifyName Enclave API.

### Are there any changes between the method of setting up the Enclave server environment variables in the VerifyNAME solution and the TravelRule solution?

The required fields for the enclave server environment variables to initiate the enclave server are slightly different. You can check the required fields for each environment variable via the link below.

* The environment variables of TravelRule solution: [Enclave setup](ref:travelrule-enclave-setup)
* The environment variables of VerifyName solution [Enclave setup](verifyname-enclave-setup)