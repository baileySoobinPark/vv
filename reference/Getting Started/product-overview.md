---
title: Product Overview
deprecated: false
hidden: true
metadata:
  robots: index
---
# VerifyVASP Architecture

## Overview

VerifyVASP operates with a decentralized architecture, consisting of a Central Server and individual Enclave Servers installed by each VASP (Virtual Asset Service Provider).

***

## Central Server

The Central Server, operated by VerifyVASP, acts as a message mediator between VASPs.\
It is necessary to configure the appropriate Central Server endpoint for your Enclave Server's environment.

* **Production Endpoint**:\
  `https://api.vega-protocol.com`

* **Staging Endpoint**:\
  `https://api.vega-protocol.xyz`

<br />

> 📘 **Environment Variable**
>
> Set the selected Central Server URL in the Enclave Server’s environment variable:

<br />

## Enclave Server

Each VASP must install an Enclave Server within their own infrastructure to communicate with the Central Server.

* The enclave server is developed by VerifyVASP and distributed as a docker image.
* The docker image for the enclave server is shared on the Private docker hub registry.
  * Send your Docker Hub ID to [corporate@verifyvasp.com](mailto:corporate@verifyvasp.com) to get the download access.
* An integrated database, along with several tables needs to be created for the enclave server.
* VASP's own application servers communicates with the installed enclave server.