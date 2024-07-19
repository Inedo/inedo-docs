---
title: "Downloads & Release Notes"
order: 3
---

The Inedo Agent can be installed to Windows servers using an `installer exe` file (for GUI-based and silent installation) or with a `manual zip` file. See [Inedo Agent Installation](/docs/inedo-agent/inedoagent-installation-installation-guide) to learn more.

There are no minimum server requirements, and all modern Windows versions (7+/2008+) are supported. We recommend using the latest version of the installer, which you can download here:

<a href="http://cdn.inedo.com/downloads/inedo-agent/InedoAgentSetup.exe" style=" background:#025291;color:#ffffff;padding: 6px 20px;  border-radius: 3px;font-size: 14pt;text-decoration:none">Download Inedo Agent Installer (latest)</a>

You can also download older versions if needed.

## Supported Versions
The following versions of the Inedo Agent are supported; you should install the latest version unless there is a specific issue with an older version.

| Version           | Downloads          | Notes
| ----------------- | ------------------ |------
| 51 (Aug 4, 2023) | [installer exe](http://cdn.inedo.com/downloads/inedo-agent/InedoAgentSetup.51.exe) &#124; [manual zip](http://cdn.inedo.com/downloads/inedo-agent/InedoAgent.51.zip) | <ul><li>reliability of Agent updates</li></ul>
| 46 (Dec 20, 2019) | [installer exe](http://cdn.inedo.com/downloads/inedo-agent/InedoAgentSetup.46.exe) &#124; [manual zip](http://cdn.inedo.com/downloads/inedo-agent/InedoAgent.46.zip) | <ul><li>[IAGT-23](https://issues.inedo.com/youtrack/issue/IAGT-23) - FIX: Agent impersonation may fail when impersonating a non-administrator account</li></ul>
| 45 (Dec 4,  2019) | [installer exe](http://cdn.inedo.com/downloads/inedo-agent/InedoAgentSetup.45.exe) &#124; [manual zip](http://cdn.inedo.com/downloads/inedo-agent/InedoAgent.45.zip) | <ul><li>[IAGT-21](https://issues.inedo.com/youtrack/issue/IAGT-21) - FIX: Starting a process with different credentials may fail when Agent service is running as Local System</li><li>[IAGT-22](https://issues.inedo.com/youtrack/issue/IAGT-22) - FIX: Impersonation fails when specifying a domain account in domain\user format</li></ul>
| 44 (Aug 7,  2019) | [installer exe](http://cdn.inedo.com/downloads/inedo-agent/InedoAgentSetup.44.exe) &#124; [manual zip](http://cdn.inedo.com/downloads/inedo-agent/InedoAgent.44.zip) | <ul><li>[IAGT-10](https://issues.inedo.com/youtrack/issue/IAGT-18) - FIX: Instance ID is not always respected in Agent commands</li></ul>

## Unsupported & Discontinued Versions
We do not recommend using an unsupported version and advise upgrading when possible.

### Upgrade Advisory for v49/v50 & Older BuildMaster Versions<a name="upgrade-advisory"></a>
:::(Warning)
If you're using older versions of BuildMaster 7.0 or BuildMaster 2022 with v49/v50 of the Inedo Agent, we recommend upgrading BuildMaster and/or the agent as soon as possible.
:::

Version 50 (May 6,  2022) and version 49 (Jan 20, 2021) have a critical bug where an improperly-formatted encryption value in the [agent configuration file](/docs/inedo-agent/maintenance-configuration/inedoagent-configuration-configuration-file) will cause the agent's encryption mode to fallback to `none` instead of crash. A lack of encrypted communication could be exploited by a malicious actor using a MITM (man-in-the-middle) attack to escalate privileges on the machine.

BuildMaster 7.0.29+ and BuildMaster 2022.14+ will attempt automatically work around these issues by repairing the agent configuration file on connected servers and then restarting the Inedo Agent. If the configuration file cannot be updated for some reason, BuildMaster will advise you to upgrade the agent.

### Unsupported Version List & Change Notes

| Version           |  Notes
| ----------------- |------
| 50 (May 6,  2022) |<ul><li>reliability of Agent updates</li></ul>
| 49 (Jan 20, 2021) | <ul><li>Support for outgoing connection mode</li><li>Automatic instancing based on source address</li><li>New configuration file format</li></ul>
| 43 (Jul 19, 2019) | <ul><li>[IAGT-3](https://issues.inedo.com/youtrack/issue/IAGT-3) - FIX: Agent version in registry is not updated during automatic agent upgrades</li><li>[IAGT-4](https://issues.inedo.com/youtrack/issue/IAGT-4) - Allow larger encryption keys for AES encryption mode</li><li>[IAGT-9](https://issues.inedo.com/youtrack/issue/IAGT-9) - FIX: Installer should not change encryption key if the configuration already exists</li><li>[IAGT-10](https://issues.inedo.com/youtrack/issue/IAGT-10) - Update Logo to use Inedo glyph instead of Otter glyph</li><li>[IAGT-14](https://issues.inedo.com/youtrack/issue/IAGT-14) - Implement agent protocol v2</li><li>[IAGT-15](https://issues.inedo.com/youtrack/issue/IAGT-15) - Add multi-instance support</li><li>[IAGT-16](https://issues.inedo.com/youtrack/issue/IAGT-16) - Add isolation/impersonation support</li><li>[IAGT-17](https://issues.inedo.com/youtrack/issue/IAGT-17) - FIX: Validate current agent name in .current file after it is read</li></ul>
| 42 (Mar 4,  2019) | <ul><li>[IAGT-12](https://issues.inedo.com/youtrack/issue/IAGT-12) - FIX: Unhandled exceptions during command deserialization can cause agent to crash</li></ul>
| 41 (Sep 27, 2018) | <ul><li>[IAGT-5](https://issues.inedo.com/youtrack/issue/IAGT-5) - Target .NET 4.5.2</li><li>[IAGT-6](https://issues.inedo.com/youtrack/issue/IAGT-6) - FIX: Handshake connection timeouts may not expire properly</li><li>[IAGT-7](https://issues.inedo.com/youtrack/issue/IAGT-7) - Allow more than one client to handshake with the server at one time</li><li>[IAGT-8](https://issues.inedo.com/youtrack/issue/IAGT-8) - Add HandshakeTimeout configuration value</li></ul>
| 40 (Jan 18, 2018) | <ul><li>[IAGT-2](https://issues.inedo.com/youtrack/issue/IAGT-2) - FIX: Operations hang if agent process exits before they complete</li></ul>
| 39 (Nov 8,  2018) | <ul><li>[IAGT-1](https://issues.inedo.com/youtrack/issue/IAGT-1) - FIX: Possible leaked memory mapped file pointer when resources are released via finalizer</li></ul>
| 38 (Aug 14, 2017) | <ul><li>Fix v37 regression (may crash after idle timeout period</li></ul>
| 37 (Aug 14, 2017) | <ul><li>Potential fix for memory leaks</li></ul>
| 36 (Jul 6,  2017) | <ul><li>Agent installer change to better report errors</li></ul>
| 35 (Sep 16, 2016) | <ul><li>Add SessionID to error log</li></ul>
| 34 (Aug 14, 2016) | <ul><li>BuildMaster support and various improvements including Healthcheck</li></ul>
| 20 (May 24, 2016) | <ul><li>Minor installer bugfix for silent installation</li></ul>
| 17 (Dec 23, 2015) | <ul><li>First public version</li></ul>
