---
title: ProGet's Configuration Files
subtitle: Configuration Files
keywords: proget, installation
---

Starting with release 5.1.0, ProGet uses a common configuration file path for its web and service components. Previously, the information stored in this file was duplicated in the Web.config and the ProGet.Service.exe.config files.

## Configuration File Location {#config-location data-title="Configuration File Location"}

ProGet looks for its configuration file at: ``%PROGRAMDATA%\Inedo\SharedConfig\ProGet.config.`` On most systems, this will resolve to `C:\ProgramData\Inedo\SharedConfig\ProGet.config`.

## Configuration File Format {#config-format data-title="Configuration File Format"}

The ProGet configuration file is a simple XML format:

```
<InedoAppConfig>
  <ConnectionString>SQL Server database connection string</ConnectionString>
  <EncryptionKey>32-character hex key for stored secrets</EncryptionKey>
  <WebServer Enabled="true/false" Urls="web server listen URLs" />
</InedoAppConfig>
```

The **ConnectionString** element contains the SQL connection string used by ProGet.

The **EncryptionKey** element is optional and if specified, will be used to encrypt stored credentials for remote resources.

The **WebServer** element controls the integrated web server. If you are using IIS, the **Enabled** attribute should be set to **false**. If **Enabled** is set to **true**, then the **Urls** attribute should contain URLs to listen to (for example <span style="display: inline;">http://my.local.proget:80/;http://localhost:1000/</span>).

## Legacy Configuration {#legacy-config data-title="Legacy Configuration"}

Previous versions of ProGet stored the connection string and encryption key in Web.config and ProGet.Service.exe.config. These values can still be defined in these files for compatibility purposes, and if present will override what is specified in the "shared" config file.
