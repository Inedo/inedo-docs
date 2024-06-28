---
title: "Security Requirements"
order: 2
---

## User Accounts

The Inedo Agent runs as a service on Windows, and can be configured to run under any user account. By default, it is installed to run with Local System privileges, which gives full access to the local system. This level of access is often required for deployment and orchestration jobs, but it does mean that the agent will have permission to do anything on the server. We recommend using a local administration account that has no access to network/domain resources for most scenarios.

In more tightly controlled environments, you may choose run the Inedo Agent as a more limited access user. This will work, provided that user account has access to all the resources you will need the agent to manage, but there are some limitations to limited-access (non-admin) accounts:

  - No user account impersonation
  - No upgrades direct from BuildMaster/Otter unless the user account is given permission to overwrite the agent's install directory. Agent upgrades are infrequent, so this is usually a managable limitation.
    - Upgrades can still be performed by running the agent installer manually

## Network Considerations

When running in listener mode, the Inedo Agent will accept all requests that have been encrypted with the configured AES security key. However, if you do not configure an AES security key, this means the agent will accept any and all requests; thus, it's quite important to use and treat your AES security tokens as carefully as you would passwords to access the server - essentially, that's what they are.

As an added level of security, we recommend restricting incoming connections to known IP addresses either using a firewall or the `AllowedAddresses` field in the agent's configuration file, especially if your server is exposed to the Internet.