---
title: "Upgrading Agents for BuildMaster 4.0"
order: 2
---

::: (Warn) (Outdated Content (BuildMaster v3))
This document only applies to users upgrading from BuildMaster v3 and is a companion to [Upgrading from BuildMaster v3 and v4](/docs/buildmaster/installation-maintenance/buildmaster-upgrading/upgrading-from-buildmaster-v3-and-v4)
:::

For BuildMaster 4.0, we took advantage of the major version change to make some changes that prevent BuildMaster 3.x from automatically updating agents:

- BuildMaster agents now require .NET 4.0 instead of .NET 2.0
- The self-hosted WCF agent SOAP (deprecated in favor of the self-hosted TCP agent) has been removed
- The Linux Mono-based SOAP agent (deprecated in favor of SSH-based agents) has been removed

### Upgrading an IIS-Hosted SOAP Agent

This type of agent is still officially supported, but requires manual reinstallation:

1. Uninstall the old agent from the remote server – **do not delete it from the Servers page within BuildMaster**
2. Ensure that .NET 4 is installed and ASP.NET 4 is registered with IIS
3. Install the new agent

### Upgrading a Self-Hosted TCP Agent

This type of agent is still officially supported, but requires manual reinstallation:

1. Uninstall the old agent from the remote server – **do not delete it from the Servers page within BuildMaster**
2. Ensure that .NET 4 is installed
3. Install the new agent

### Upgrading a Self-Hosted SOAP (WCF) Agent

This type of agent has been deprecated and removed from agent installers since BuildMaster 3.3, but BuildMaster has continued to keep existing agents of this type current in versions prior to 4.0.

1. Choose which type of agent to use instead. See [Upgrading Legacy BuildMaster Agents to The Inedo Agent](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features/buildmaster-upgrading-to-v5-agent-comparison) 
   for a comparison of the agent types.
2. Depending on which agent you have chosen, follow the steps in one of the above sections of this article to remove the old agent and install the new one.
3. Follow the "Converting from Self-Hosted SOAP (WCF) to the Self-Hosted TCP Agent" instructions below to change the agent type from BuildMaster's perspective.

### Upgrading a Linux Mono Agent

This type of agent has been deprecated since BuildMaster 3.2 in favor of an SSH-based agent that requires no installed components on the Linux server.

1. Remove the Mono agent from your Linux server.
2. Ensure that your server is configured to accept SSH connections.
3. Add an SSH-based agent to BuildMaster.

## Converting from Self-Hosted SOAP (WCF) to the Self-Hosted TCP Agent

Since the self-hosted SOAP (WCF) agent was removed in BuildMaster 4.0, it must be uninstalled and the new self-hosted TCP agent installed on the remote server as a replacement. **Note: you should not delete the agents from within BuildMaster itself, otherwise you would need to update all deployment plans that rely on that server!**

Once you have installed the new self-hosted TCP agent on the remote server, you will need to convert your agents on the BuildMaster side. To do so, visit the Administration overview page, select "Execute a System Recipe", choose the "Change Agent Types (TCP-SOAP)" option, and leave the default dropdown option of "Agent Conversion Types: From SOAP to TCP Agent". By default, only SOAP agents are shown – so if you do not see the server in the list, it is already a TCP-based agent. **Note: be sure to only choose servers with self-hosted (i.e. Windows Service hosted) SOAP agents, if you are hosting your agents with IIS leave them unchecked so they remain SOAP agents.**

Once you've selected the correct SOAP agents to convert, choose "Convert Agents" to convert them and return to the server overview page:

![image](/resources/docs/WindowsLiveWriter_UpgradingAgentsforBuildMaster4.0_BFD2_image_thumb_1.png)

Once converted, it can take up to a few minutes to update the server overview page because the status values on that page are cached. To check the live status of a server, select the server from the overview page, and examine the Agent Status section at the bottom of that page.

![image](/resources/docs/WindowsLiveWriter_UpgradingAgentsforBuildMaster4.0_BFD2_image_thumb_2.png)

Note also that the Hosted property only indicates that the type of agent (in this case, the self-hosted TCP-based agent) is installed on the server—it does not know whether this is actually the case or not unless the Status field indicates something other than Error or Inactive.

### Troubleshooting Agents in ERROR Status

Agents can be put in ERROR status for a variety of reasons, the most common are:

- The wrong type of agent is installed on the server—verify that the type of agent that BuildMaster identifies, is actually what is installed on the server (e.g. Self-hosted (TCP) Agent should have a service installed on the remote server, and IIS-hosted (SOAP) Agent should have a website configured in IIS on the remote server). This problem commonly results in error messages in the BuildMaster Error Log indicating such a problem.
- For the self-hosted agent, the agent service (INEDOBMAGT) is not running
- For the IIS agent, either the hosting website or application pool is stopped
- There is a firewall blocking the port of the agent
- There is a newer agent installed on the remote server than the current installation of BuildMaster. Always make sure to match the version of the agent with that of the BuildMaster installation when freshly installing or manually upgrading the agent

#### Testing connection to an IIS-Hosted (SOAP) agent:

To test whether a SOAP agent is accessible by BuildMaster, browse to **http://agent-server:port/agent.asmx** from the BuildMaster server. If you see a page similar to the image below, the connection is available:

![image](/resources/docs/WindowsLiveWriter_UpgradingAgentsforBuildMaster4.0_BFD2_image_thumb_3.png)

If you do not see this page, try connecting locally from the agent server itself—this will indicate whether the network is the culprit.

#### Testing connection to a Self-Hosted (TCP) agent:

To whether a TCP agent is accessible by BuildMaster, open a telnet session to the server and port:

telnet agent-server

If the connection is successful, an empty prompt appears and you can send a text to the agent. If the connection is not successful, an error message similar to the following is displayed: "Connecting To agent-server...Could not open a connection to the host, on port 1000: Connect failed".
