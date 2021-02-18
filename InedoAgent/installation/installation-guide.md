---
title: Installation Guide
subtitle: Installation Guide
sequence: 10
keywords: inedo, inedo agent, installation

---
## Using the Installer {#installer data-tile="Using the Installer"}

 ### 1. Pre-Installation Check List {#preinstall data-title="1. Pre-Installation Check List"}

 There are no intense server requirements, and support all modern version of Windows (7+/2008+). Before installing, consider the following:

{.docs}
- **.NET Framework 4.5.2** – Requires .NET 4.5.2 or later; this is already installed on Windows Server 2012, and you can [download it from microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=30653) on earlier servers
- **Firewall (Inbound)** – the agent will listen on the port of your choosing

### 2. Download Inedo Agent {#download data-title="Download Otter Windows Agent"}

Download the latest Inedo Agent from the [Agent Versions & Release Notes](/docs/inedoagent/versions) page.

### 3. Target Directory {#target-directory data-title="Target Directory"}

Although you may install wherever you like, it is recommended to keep the temporary directory short to avoid potential issues with tools that have problems with long paths.

### 4. User Account {#user-account data-title="User Account"}

By default, the agent service will run as [LocalSystem](https://msdn.microsoft.com/en-us/library/windows/desktop/ms677973(v=vs.85).aspx), which is an administrative service account. While not required, administrator-level privileges are strongly recommended as it will eliminate any difficulty or confusion with moving files and running different operations.

### 5. Network {#network data-title="Network"}

We recommend using the default port (46336), as that's Inedo's [officially designated port](http://iana.org/assignments/port-numbers). Make sure that the selected port is not blocked by a firewall.

You can change this later by editing the configuration file in the installation directory, and restarting the service.

### 6. Encryption {#encryption data-title="Encryption"}

Inedo's agents use a FIPS-compliant symmetric AES encryption scheme by default, and the installer will automatically generate a secure, 32-digit key for you. You can use that key, or another one of your choosing.

If you select SSL instead of built-in AES, then you will need to have a valid certificate installed on the server (see [KB#1040](https://inedo.com/support/kb/1040/securing-self-hosted-agents-using-ssl)).

**No Encryption** is also an option, but we don't recommend using it. The primary reason we have this is so that you can use a tool like [Wireshark](https://www.wireshark.org/) to inspect the individual messages, either as directed by our support engineers or because you really like studying binary TCP protocols.{.announcement}

You can change this later by editing the configuration file in the installation directory, and restarting the service.

### 7. Summary {#summary data-title="summary"}

Verify that the information here is correct and then click Install.

### 8. Installation {#installation data-title="Installation"}

The actual installation is near instantaneous, and should take no more than a minute or two, even on the slowest of systems.

Once the installation is complete, just close the installer or click the Exit button.

## Installing Multiple Agents on Same Machine {#multiple data-title="Installing Multiple Agents on Same Machine"}

Sometimes, it may be desired to install multiple instances of the Inedo Agent on the same machine. The most common reason for this is privileges; one agent may run in a higher privilege mode than the other.

:::attention {.technical}
The agent installer (including silent installation) does not currently support installation or management of multiple instances. See [KB#1146](https://inedo.com/support/kb/1146/installing-multiple-instances-of-inedoagent-on-a-single-machine) for guidance on how to install more than one agent on the same server manually.
:::

If you'd just like to connect multiple instances of BuildMaster or Otter to the same agent, you can use
[agent instancing](instancing) instead of installing multiple agents.