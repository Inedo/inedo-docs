---
title: "Manual Installation"
order: 4
---

The Inedo Agent installer may installed manually, which may be preferred over [silent installation](/docs/inedo-agent/inedoagent-installation-installation-guide/inedoagent-installation-silent-installation) for script-based or infrastructure-as-code installations.

:::(Error) (Manual Installation is an Advanced Option)
We generally don't recommend manual installation because it's more complicated to maintain, and it may not be obvious to other system administrators that the Inedo Agent is installed on a server. If you chose to use this option, make sure to thoroughly document it internally.
:::

## Manual Installation Package

The manual installation package is a `.zip file` that contains all the files to install or upgrade the Inedo Agent. To download the package, select "Manual Install" link for the desired version from the [Inedo Agent Versions](/docs/inedo-agent/inedoagent-versions). 

If you are unsure which version to select, use the latest version.
 
## Installation Directories 

By default, the Inedo Agent installer allows configuration of its root installation directory, and will configure temporary directories for you. During a manual installation, these directories must be provisioned prior to installation.

Before configuring the necessary paths, you should decide where the following base directories will reside:

| Directory | Description | Installer Default |
|--|--|--|
| Root installation directory | the root disk path for the Website and Service binaries, and extensions | `C:\Program Files\InedoAgent` |

Though not strictly required until later, you may also want to ensure the existence of the following directories if you would like to use the installer defaults:
 - `C:\ProgramData\Inedo\SharedConfig`

## How to Manually Install

### 1. Ensure the Inedo Agent configuration file exists with valid values

The first step is to create or update the [Inedo Agent Configuration File](/docs/inedo-agent/maintenance-configuration/inedoagent-configuration-configuration-file) to supply the temporary paths and connection information.

When using AES Encryption, the Key is a 32-character hex string is used for AES encryption. You can generate this however you'd like. An easy way to do this is to search "generate random hex string" online. 

:::(Info) (Note)
The port you specify to listen on must be allowed in the Windows Firewall.
:::

### 2. Copy Inedo Agent Files

Copy the contents from the installation package to a subdirectory of the *root installation directory* identified earlier. For reference, the installer defaults this to: `C:\Program Files\InedoAgent`

### 3. Install the Inedo Agent Service

The copied files will contain the service executable `InedoAgentService.exe`; run this program from an elevated command prompt or PowerShell window to install the Windows Service (`INEDOAGENTSVC`):

```
PS C:\Program Files\InedoAgent> .\InedoAgentService.exe install --user="InedoAgentServiceUser@domain" --password="<account-password>"
```

If you want the Inedo Agent to run as the `NT AUTHORITY\LocalSystem` user, you can omit the `--user` and `--password` parameters.

The `install` command will simply register the Inedo Agent as a Windows service using the default/recommended options, including using the `INEDOAGENTSVC` service name. You can use `sc.exe` or another means to install/register the service if you prefer.

### 4. Start the service

The service can be started from the Windows Service Controller, or using the following PowerShell command:

```
Start-Service INEDOAGENTSVC
```

## Installing In Outbound-communication Mode 

As of v49, the Inedo Agent supports an outbound communication mode, so that it can connect to a product instance rather than only wait for incoming connections. This may be useful for when an agent is hosted on local infrastructure, but the product is hosted in a cloud service.

Please see our guide to [configure for outbound mode](/docs/inedo-agent/maintenance-configuration/inedoagent-configuration-outbound-mode).

## Installing Multiple Agents on Same Machine 

By following these instructions, it's technically possible to install multiple instances of the Inedo Agent on the same machine. You will just need to register the service with a different name, such as `INEDOAGENTSVC2`.

:::(Warning)
There are very few reasons to install multiple agents on the same machine and it's generally not recommended. 
:::

The most common use case for multiple agents is privilege isolation: one agent may run in a higher privilege mode than the other. However, because you can execute OtterScript with different credentials in BuildMaster and Otter, this is rarely needed.

If you'd just like to connect multiple instances of BuildMaster or Otter to the same agent, make sure to use Inedo Agent v49 or later; there is no need to install additional agents if you need to connect with multiple instances of BuildMaster or Otter.

Installing multiple agents as an attempt to improve performance will generally have the opposite effect. The Inedo Agent already works in parallel, and uses several worker processes.
