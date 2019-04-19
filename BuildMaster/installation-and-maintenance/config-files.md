---
title: BuildMaster's Configuration Files
subtitle: Configuration Files
keywords: buildmaster, installation

---
Starting with release 6.0.7, BuildMaster uses a common configuration file path for its web and service components. Previously, the information stored in this file was duplicated in the Web_appSettings.config and the App_appSettings.config files.

## Configuration File Location {#config-file data-title="Configuration File Location"}

BuildMaster looks for its configuration file at: ``%PROGRAMDATA%\Inedo\SharedConfig\BuildMaster.config.`` On most systems, this will resolve to `C:\ProgramData\Inedo\SharedConfig\BuildMaster.config.`

## Configuration File Format {#config-file-format data-title="Configuration File Format"}

The BuildMaster configuration file is a simple XML format:

```
<InedoAppConfig>
  <ConnectionString>SQL Server database connection string</ConnectionString>
  <EncryptionKey>32-character hex key for stored secrets</EncryptionKey>
  <WebServer Enabled="true/false" Urls="web server listen URLs" />
</InedoAppConfig>
```

The **ConnectionString** element contains the SQL connection string used by BuildMaster.

The **EncryptionKey** element is optional and if specified, will be used to encrypt
stored credentials for remote resources.

The **WebServer** element controls the integrated web server. If you are using IIS,
the **Enabled** attribute should be set to **false**. If **Enabled** is set to
**true**, then the **Urls** attribute should contain URLs to listen to (for
example <span style="display: inline">http://my.local.BuildMaster:80/;http://localhost:1000/</span>).

## Legacy Configuration {#legacy-config data-title="Legacy Configuration"}

Previous versions of BuildMaster stored the connection string and encryption key in Web_appSettings.config and App_appSettings.config. These values can still be defined in these files for compatibility purposes, and if present will override what is specified in the "shared" config file.
