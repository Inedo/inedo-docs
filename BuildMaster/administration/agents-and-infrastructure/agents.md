---
title: Managing Agents and Servers
subtitle: Agents and Servers
keywords: buildmaster, servers, agents
sequence: 30
show-headings-in-nav: true
---

To deploy releases to your servers, BuildMaster needs to be able to communicate with those servers over a fast and secure channel. There are a few options for doing this.

## Windows Servers {#windows data-title="Windows Servers"}

The [Inedo Agent](/docs/inedoagent/overview) is generally the best way to communicate with a Windows server. It's light-weight, and uses a highly-optimized and resilient protocol built solely for this purpose, and is [quite easy to install](/docs/buildmaster/installation-and-maintenance/installation-guide/agent-installation-guide).

## Agentless Windows Servers {#agentless data-title="Agentless Windows Servers"}

Alternatively, BuildMaster can use PowerShell Remoting to communicate with Windows servers; however, this is generally slower and less resilient than the Inedo Agent protocol. You can connect using integrated authentication (i.e. whatever account the service is running under), or with a username & password [resource credential](/docs/buildmaster/administration/resource-credentials). See [Enable-PSRemoting](https://technet.microsoft.com/en-us/library/hh849694.aspx) to configures a server to receive remote commands.

## Linux Servers {#linux data-title="Linux Servers"}

To communicate with Linux servers, BuildMaster uses the lightweight, highly-optimized, and resilient protocol already enabled on nearly every Linux box: SSH and SFTP. You can connect with a private key or username & password [resource credential](/docs/buildmaster/administration/resource-credentials).

## Local Agents {#local data-title="Local Agents"}

If you're using BuildMaster to interact with the server it's installed on, you can just set it up using a local agent. This uses the same process/identity that the [service](/docs/buildmaster/installation-and-maintenance/architecture/service) is hosted as, and doesn't have very many privileges by default.

## Automatic Inedo Agent Updates {#automatic-updates data-title="Automatic Inedo Agent Updates"}

Although the Inedo Agent is both backwards- and forwards-compatible, you may wish to update it on the servers to fix bugs and improve performance. This can be done manually (by running a newer installer on a remote server), or by using the agent update process built in to BuildMaster.

When BuildMaster performs a routine check on the connected agents, the installed Inedo Agent version is captured, and can be viewed on the server overview page. When you visit the server list page, if any servers have a version of the Inedo Agent that can be updated, you will be prompted to upgrade agents.

Clicking on the Update Agents button will open a dialog that gives you the option to select which servers to update, as well as where to download the new Inedo Agent files from.

:::attention {.best-practice}
Self-updating software always has a risk of failing mid-update. If this happens with the Inedo Agent, you will need to log on to the server to reinstall the agent. Therefore, only update agent versions when you are prepared to also do a manual update if absolutely necessary.
:::

Clicking *Update Agent* will initiate an execution that downloads the installer and updates the specified servers with a new agent.
