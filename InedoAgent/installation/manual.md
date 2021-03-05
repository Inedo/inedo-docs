---
title: Manual Installation
sequence: 40
show-headings-in-nav: true
---

::: {.attention .best-practice} 
**Note:** agents should be installed on remote servers, and are not required to be installed on the Otter or BuildMaster server itself. 
:::

## Prerequisites {#prerequisites data-title="Prerequisites"}

### Pre-Installation Check List

There are no intense server requirements, and support all modern version of Windows (7+/2008+). Before installing, consider the following:

{.docs}
- **.NET Framework 4.5.2** – Requires .NET 4.5.2 or later; this is already installed on Windows Server 2012, and you can [download it from microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=30653) on earlier servers
- **Firewall (Inbound)** – the agent will listen on the port of your choosing (default 46336)


### Get the Manual Installation Package {#get}

The manual installation package is a .zip file that contains all the files to create or upgrade the Inedo Agent.

To download the package, select "Manual Install" link for the desired version from the [Inedo Agent Versions](/docs/inedoagent/versions). If you are unsure which version to select, use the latest version.

 
### Installation Directories {#directories}

By default, the Inedo Agent installer allows configuration of its root installation directory, and will configure temporary directories for you. During a manual installation, these directories must be provisioned prior to installation.

Before configuring the necessary paths, you should decide where the following base directories will reside:

| Directory | Description | Installer Default |
|--|--|--|
| Root installation directory | the root disk path for the Website and Service binaries, and extensions | `C:\Program Files\InedoAgent` |

Though not strictly required until later, you may also want to ensure the existence of the following directories if you would like to use the installer defaults:

{.docs}
 - `C:\ProgramData\Inedo\SharedConfig`

## Install the Inedo Agent {#install-agent data-title="Install Agent"}

### Prerequisites

Before installing the Inedo Agent, the following features must be enabled in Windows:

#### Features

{.docs}
 - .NET Framework 4.6 Features > .NET Framework 4.6

### 1. Ensure the Inedo Agent configuration file exists with valid values

The first step is to create or update the [Inedo Agent Configuration File](/docs/inedoagent/configuration/configuration-file) to supply the temporary paths and connection information.

When using AES Encryption, the Key is a 32-character hex string is used for AES encryption. You can generate this however you'd like, including by copying the pseudorandom string below, which is regenerated every time you refresh this page:

<pre id="random-aes-key"></pre>
<script>
var aesKey;
if (window.crypto) {
    if (crypto.getRandomValues){
        aesKey = new Uint8Array(16);
        crypto.getRandomValues(aesKey);
        aesKey = aesKey.reduce(function(str, byte) {
            return str + ('0' + byte.toString(16)).substr(-2);
        }, '').toUpperCase();
    } else {
        aesKey = Array(32).map(function() {
            return Math.floor(Math.random() * 16).toString(16);
        }).join('').toUpperCase();
    }
} else {
    aesKey = Array(32).map(function() {
        return Math.floor(Math.random() * 16).toString(16);
    }).join('').toUpperCase();
}
document.getElementById('random-aes-key').textContent = aesKey;
</script>

::: {.attention .best-practice}
Note: The port you specify to listen on must be allowed in the Windows Firewall.
:::

### 2. Copy Inedo Agent Files

Copy the contents from the installation package to a subdirectory of the *root installation directory* identified earlier. For reference, the installer defaults this to: `C:\Program Files\InedoAgent`

### 3. Install the Inedo Agent Service

The copied files will contain the service executable `InedoAgentService.exe`; run this program from an elevated command prompt or PowerShell window to install the Windows Service (`INEDOAGENTSVC`):

```
PS C:\Program Files\InedoAgent> .\InedoAgentService.exe install --user="InedoAgentServiceUser@domain" --password="<account-password>"
```

If you want the Inedo Agent to run as the `NT AUTHORITY\LocalSystem` user, you can omit the `--user` and `--password` parameters.

::: {.attention .technical}
Note: at this time, it is not permitted to change the service name from `INEDOAGENTSVC`.
:::

### 4. Start the service

The service can be started from the Windows Service Controller, or using the following PowerShell command:

```
Start-Service INEDOAGENTSVC
```

## Installing In Outbound-communication Mode {#multiple data-title="Outbound Communication Mode"}

As of v49, the Inedo Agent supports an outbound communication mode, so that it can connect to a product instance instead of only wait for incoming connections. This may be useful for when an agent is hosted on local infrastructure, but the product is hosted in a cloud service.

Please see our guide to [configure for outbound mode](https://docs.inedo.com/docs/inedoagent/configuration/outbound-mode).
