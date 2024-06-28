---
title: "Configuring the Inedo Agent"
order: 1
---

As of v49, the Inedo Agent stores its configuration in the `InedoAgent.config` file. Earlier versions stored configuration in the `InedoAgentService.exe.config` file, located in the agent's installation directory.

## Configuration File Location

Normally, the Inedo Agent looks for its configuration file at: ``%PROGRAMDATA%\Inedo\SharedConfig\InedoAgent.config.`` On most systems, this will resolve to `C:\ProgramData\Inedo\SharedConfig\InedoAgent.config`. However, to accomodate upgrades and other scenarios it can load from other locations:

  1. `InedoAgent.config` in install directory
  2. Legacy `InedoAgentService.exe.config` in install directory
  3. `InedoAgent.config` in `%PROGRAMDATA%\Inedo\SharedConfig`

If no configuration file is found in any of those locations, the Inedo Agent will not run.


## Configuration File Format 

The Inedo Agent configuration file is a simple XML format:

```
<InedoAgentConfig>
  <LogFile></LogFile>
  <ExtensionCachePath></ExtensionCachePath>
  <BuildMasterRootPath></BuildMasterRootPath>
  <OtterRootPath></OtterRootPath>
  <Listener Enabled="true/false">
    <Encryption Mode="none/ssl/aes">
      <CertificateName></CertificateName>
      <Key></Key>
    </Encryption>
    <Address></Address>
    <Port></Port>
    <MaxConcurrentHandshakes></MaxConcurrentHandshakes>
    <HandshakeTimeout></HandshakeTimeout>
    <AllowedAddresses></AllowedAddresses>
  </Listener>
  <Connections Enabled="true/false">
    <Server Host="" Key="" />
  </Connections>
</InedoAgentConfig>
```

|Name|Default Value|Notes|
|--- |--- |--- |
|LogFile|*none*|When a path to a file is specified, diagnostic information is written to this file. Do not specify a log file unless directed to do so by Inedo support.|
|ExtensionCachePath|%TEMP%\Inedo\ExtensionCache|Directory where Inedo extensions are unpacked and loaded from when necessary.|
|BuildMasterRootPath|%TEMP%\InedoAgent\BuildMaster|Acts as the root temporary directory for BuildMaster deployments that target this server|
|OtterRootPath|%TEMP%\InedoAgent\Otter|Acts as the root temporary directory for Otter orchestrations that target this server|
|Listener->Enabled|true|When true, the agent will listen for incoming connections.|
|Listener->Encryption->Mode|none|Specifies the type of encryption used for incoming connections. May be none, ssl, or aes.|
|Listener->Encryption->CertificateName|*none*|When Encryption Mode is `ssl`, specifies the name of the certificate used for incoming connections.|
|Listener->Encryption->Key|*none*|When Encryption Mode is `aes`, specifies the 128 or 256-bit key in 64 hexadecimal digits used to encrypt data from incoming connections.|
|Listener->Address|Any|The IP address to listen for incoming connections on.|
|Listener->Port|46336|The TCP port to listen for incoming connections on.|
|Listener->MaxConcurrentHandshakes|100|Maximum number of simultaneous incoming handshakes to process.|
|Listener->HandshakeTimeout|30|Maximum amount of time in seconds to allow an incoming handshake to take.|
|Listener->AllowedAddresses|*any*|;-separated list of IP addresses that are allowed to connect to this agent.|
|Connections->Enabled|true|When true, connections will be issued to the servers specified in this element.|
|Connections->Server->Host|*none*|Remote host name/address and optionally port if not using 46336 of product to connect to.|
|Connections->Server->Key|*none*|Secret key defined by the remote product to identify this agent instance.|