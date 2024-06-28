---
title: "Installation Configuration Files"
order: 12
---

We generally recommend using the [Inedo Hub](/docs/installation/windows/desktophub-overview) to manage the installation configuration for your Inedo products. However, you can also edit the configuration files directly.

:::(info)
This applies to **BuildMaster 6.0.7+, ProGet 5.1.0+, and Otter 2.0.9+.** See [Legacy Configuration Files](#legacy-configuration-files) if you're using an older version.
:::

## Installation Configuration Files
Inedo products installed on Windows use a common configuration file path for their web and service components. 

### Configuration File Location 
Inedo products look for a configuration file in `%PROGRAMDATA%\Inedo\SharedConfig\`. On most systems, `%PROGRAMDATA%` will resolve to  `C:\ProgramData\Inedo\SharedConfig`.

| Product | File | Example path |
| --- | --- | --- |
| **BuildMaster**  | `BuildMaster.config` | `C:\ProgramData\Inedo\SharedConfig\BuildMaster.config` |
| **ProGet** | `ProGet.config` | `C:\ProgramData\Inedo\SharedConfig\ProGet.config` |
| **Otter** | `Otter.config` | `C:\ProgramData\Inedo\SharedConfig\Otter.config` |

### Configuration File Format 
The configuration file for your Inedo product is a simple XML format:

```XML
<InedoAppConfig>
  <ConnectionString>SQL Server database connection string</ConnectionString>
  <EncryptionKey>32-character hex key for stored secrets</EncryptionKey>
  <WebServer Enabled="true/false" Urls="web server listen URLs" />
</InedoAppConfig>
```

* The **ConnectionString** element contains the SQL connection string used by your Inedo product.
* The **EncryptionKey** element is optional and if specified, will be used to encrypt stored credentials for remote resources.
* The **WebServer** element controls the integrated web server. If you are using IIS, the **Enabled** attribute should be set to **false**. If **Enabled** is set to **true**, then the **Urls** attribute should contain URLs to listen to (for example `http://my.local.BuildMaster:80/;http://localhost:1000/`).

::: (Internal) (TODO)
Add certificate information on the WebServer node doc.
:::

## Web.config and other .NET Configuration
Inedo products are built using .NET, and therefore have standard .NET configuration files for configuring the runtime environment for the web (`web.config`) and service (`«inedo-product».Service.exe.config`) components. 

We don't recommend changing these configuration files, and doing so may make your Inedo product unstable. In addition, your changes may be overwritten during an upgrade.

## Legacy Configuration Files
Previous versions of Inedo products duplicated the connection string and encryption key in two different configuration files. 

| Product | Affected versions | Connection String & EncryptionKey |
| --- | --- | --- |
| BuildMaster | 6.0.6 and earlier | `Web_appSettings.config` and `App_appSettings.config` |
| ProGet | 5.0.x and earlier | `Web.config` and `ProGet.Service.exe.config`|
| Otter | 2.0.8 and earlier |  `Web.config` and `Otter.Service.exe.config` |

These values can still be defined in these files for compatibility purposes, and if present will override what is specified in the "shared" config file. Both of these files must have the same values.
