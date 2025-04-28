---
title: Product Overview
deprecated: false
hidden: true
metadata:
  robots: index
---
# Architecture

<Image align="center" border={false} caption="VerifyVASP's Decentralized Architecture" src="https://files.readme.io/9b72de5dab5c3696f54e2505998dfa8260bc8a4aeb65f421fb3166c378101e39-VASP_Architecture.png" />

## Overview

VerifyVASP operates with a decentralized architecture, consisting of a Central Server and individual Enclave Servers installed by each VASP (Virtual Asset Service Provider).

***

## Central Server

VerifyVASP operates a central Server which serves as a message mediator server, operated by VerifyVASP. Please configure either the Production or Staging server endpoint's information below for the enclave environment variable `**VEGA_API_ENDPOINT**`.

* **Production Endpoint**:\
  `https://api.vega-protocol.com`

* **Staging Endpoint**:\
  `https://api.vega-protocol.xyz`

<br />

> 📘 **Enclave Environment Variable:**
>
> Enter the Central server URL above in `**VEGA_API_ENDPOINT**` variable.

<br />

## Enclave Server

Each VASP must install an Enclave Server within their own infrastructure to communicate with the Central Server.

* The enclave server is developed by VerifyVASP and distributed as a docker image.
* The docker image for the enclave server is shared on the Private docker hub registry.
  * Send your **Docker Hub ID** to [corporate@verifyvasp.com](mailto:corporate@verifyvasp.com) to get the download access.
* An integrated [database](ref/database) , along with several [tables]()  needs to be created for the enclave server.
* VASP's own application servers communicates with the installed enclave server.