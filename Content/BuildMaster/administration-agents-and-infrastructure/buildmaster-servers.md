---
title: "What is a \"Server\" in BuildMaster?"
order: 1
---

Buildmaster works with both Windows and Linux servers, as well as physical (bare metal) or virtual servers hosted in your own data center or in the cloud. A server in BuildMaster is essentially a connection to one of your servers, wherever it may be.

## Preparing Your Servers for BuildMaster 
BuildMaster can communicate with servers that have the [Inedo Agent](/docs/inedo-agent/inedoagent-overview) installed or use agent-less communication with PowerShell remoting (WMI) or with SSH. 

To learn about the options, see [Managing Agents and Servers](/docs/installation/agents-and-servers).

### Installing the Inedo Agent (Recommended) 
The [Inedo Agent](/docs/inedo-agent/inedoagent-overview) offers easy configuration, optimized performance with BuildMaster, and a lower risk of attack than other protocols. It can also be configured in an outbound (listener) configuration mode, which may simplify router and firewall configuration.

<a href="/docs/inedo-agent/inedoagent-versions" style=" background:#025291;color:#ffffff;padding: 6px 20px;  border-radius: 3px;font-size: 14pt;text-decoration:none">Download the Inedo Agent</a>

To install the Inedo Agent, you can follow our [step-by-step guide for the GUI-based installer](/docs/inedo-agent/inedoagent-installation-installation-guide) or use more advanced options like [silent installation](/docs/inedo-agent/inedoagent-installation-installation-guide/inedoagent-installation-silent-installation) or [manual installation](/docs/inedo-agent/inedoagent-installation-installation-guide/inedoagent-installation-manual). 



 
### Connecting with WMI (PowerShell Remoting) 
BuildMaster can also use [WS-Management](https://docs.microsoft.com/en-us/windows/win32/winrm/ws-management-protocol) (WMI).  This is the same channel that PowerShell remoting uses and is available only on the Windows platform. 

The easiest way to enable it is to use the [Enable-PSRemoting](https://technet.microsoft.com/en-us/library/hh849694.aspx) cmdlet, as follows: 

```PowerShell
Enable-PSRemoting
```

This command does many things, including: 
* Configure the WinRM service used for WMI communication 
* Create a listener to accept requests on any IP address 
* Enable a firewall exception for WS-Management communications
* Change server security to allow remote access 

After enabling WMI, BuildMaster will be able to connect to the server using a local Windows or domain account. 

#### Troubleshooting
If BuildMaster reports back an error when running a script or orchestration that looks like this:
```
WinRM cannot process the request.
The following error with error code 0x8009030e occurred while using Negotiate authentication:
A specified logon session does not exist. It may already have been terminated.
```

This has happened because the BuildMaster Service does not trust the remote PowerShell host.  To fix this, run the following PowerShell on your BuildMaster Server:
```PowerShell
Set-Item -Path WSMan:\localhost\Client\TrustedHosts -Value 'REMOTE_SERVER_NAME'
```

### Connecting with SSH (Linux) 
To communicate with Linux servers, BuildMaster uses SSH and SFTP, which almost every Linux server already has enabled. BuildMaster can use a private key or user name/password to connect.

## Adding Your Servers in BuildMaster 
Once your server has been prepared, you can add it by going to `Servers > Add Server`, or you can use the [infrastructure API](/docs/buildmaster/reference/api/infrastructure)

The Add Server menu in the BuildMaster UI will present you with three options for the agent/communication type: 
* **Inedo Agent:** Connects to an Inedo Agent that is installed and listening on a remote server 
* **PowerShell:** Connects to the remote server using PowerShell remoting 
* **SSH:** Connects to the remote server using SSH (Linux-only) 

After adding a server, you'll be able to assign a [server role](/docs/buildmaster/administration-agents-and-infrastructure/server-roles) that describes a purpose or function of the server. This will also let you deploy to server role target so that you don't have to explicitly specify server names.

## Deploying to Servers

In addition to using [Server Roles](/docs/buildmaster/administration-agents-and-infrastructure/server-roles), there are two ways to deploy to a server:

### Pipeline Stage Target

You can specify a list of servers as a stage target in a [pipeline stage](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines#pipeline-stages). When the build is deployed to that stage, the stage target's deployment plan will be run against all those servers.

### Servers and OtterScript

You can deploy to a server using a [general block](/docs/executionengine/otterscript/statements-and-blocks/general-blocks)

```
for server prod-hdars-sv1
{
    Deploy-Artifact hdars-api-service
    (
        To: C:\HDars\App\api-service
    );
}
```


## Server Variables 
Server variables are [configuration variables](/docs/otter/scripting-in-otter/otter-configuration-variables) that will be automatically used in the [OtterScript](/docs/otter/scripting-in-otter/otter-otterscript-and-operations) that runs against the target server. These configuration variables will cascade on top of any global configuration variables, but only for the server they are defined on.

Server variables can be created and modified on the Overview page of a given server.

![](/resources/docs/Server-variables-alt.png){height="" width=""}

## Servers and Environments  
[Environments](/docs/buildmaster/administration-agents-and-infrastructure/environments) are a logical grouping of Servers, and generally describes one stage of the development process (development, testing, Q/A, production, staging, etc.).  They're also used in [security and access controls](/docs/buildmaster/configuring-for-your-team/buildmaster-administration-security) to allow or restrict users from performing various tasks. 

You can associate one server with multiple environments, but [this is not recommended](/docs/buildmaster/administration-agents-and-infrastructure/environments#multiple-environments-per-server) as it can lead to unexpected behaviors.

## Resource Pools

A resource pool is a set of servers used as a single, balanced resource. You can acquire an unused server from a resource pool, then return it to the pool once the tasks you need are completed.

Visit the [Resource Pools](/docs/executionengine/overview/executionengine-components-resource-pools) documentation of the Inedo Execution Engine for more information on how to configure a resource pool.