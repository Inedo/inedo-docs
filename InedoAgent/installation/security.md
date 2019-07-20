---
title: Security Requirements
subtitle: Security Requirements
sequence: 30
keywords: inedo, inedo agent, security

---
The Inedo Agent is a standard Windows Service, which means you may configure it to run under any user context. Generally speaking, you should only give as much access to the agent's user context as is needed. This may be more complicated to set-up, so it's important to also balance the security risk with the challenge of maintaining configuration.

At a minimum, the Inedo Agent only needs permission to run as a service, have read access to its own files, full control over its data directories, and the ability to open the configured network port (default 46336). For it to be able to self-update, it will also need write access to its own files.

When running, the Inedo Agent will accept all requests that have been encrypted with the configured AES security key. However, if you do not configure an AES security key, this means the agent will accept any and all requests; thus, it's quite important to use and treat your AES security tokens as carefully as you would passwords to access the server - essentially, that's what they are.

We recommend using a domain account for the agent user context. This will make it much easier to isolate privileges, and centrally manage these accounts. You can configure this at installation, or anytime after by going to Start > Run > services.msc.

**Local System Note:** An agent with local system privileges has full access to any installed server. While this is often crucial for infrastructure orchestration (and is the default on new installations of an Inedo agent), it is discouraged for any environment where more limited access is appropriate, such as: live production environment, build servers, etc. {.announcement}

## Impersonation
As of Inedo Agent [v43](../versions), Inedo Agents can impersonate other user accounts if credentials are provided in BuildMaster/Otter.
