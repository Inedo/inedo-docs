---
title: "Installation Configuration Files"
order: 12
---

We generally recommend using the [Inedo Hub](/docs/installation/windows/inedo-hub) to manage the installation configuration for your Inedo products. However, you can also edit the configuration files directly.

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
  <PostgresConnectionString>PostgreSQL database connection string</PostgresConnectionString>
  <EncryptionKey>32-character hex key for stored secrets</EncryptionKey>
  <WebServer Enabled="true/false" Urls="web server listen URLs" ... />
</InedoAppConfig>
```

* The **ConnectionString** element contains the SQL Server connection string used by your Inedo product. For ProGet 2025+ this is not specified if using the embedded database or PostgreSQL.
* The **PostgresConnectionString** element contains the PostgreSQL/InedoDB connection string used by your Inedo product. Currently requires ProGet 2025+.
* The **EncryptionKey** element is optional and if specified, will be used to encrypt stored credentials for remote resources.
* The **WebServer** element controls the integrated web server.

#### WebServer Attributes
The `WebServer` element contains multiple attributes that controls how your Inedo product is hosted. 

| Attribute | Type | Description |
| --------- | ---- | ----------- |
| **Enabled** | `true/false` | If `true`, the Integrated Web Server is used to host ProGet, else IIS is used to host ProGet. |
| **Urls** | `string` | A list of URLs for the Integrated Web Server to listen on (ex: `http://my.local.BuildMaster:80/;http://localhost:1000/`) |
| **CertFile** | `string` | When the URLs contain a URL that starts with `https://`, this certificate file will be used for SSL communication |
| **KeyFile** | `string` | Path to Key File that contains private keys.  *This should only be used when the `CertFile` does not contain private keys.* | 
| **Password** | `string` | Password for the `CertFile` and `KeyFile` |
| **Subject** | `string` | Certificate Subject Name of a certificate stored in the Windows Certificate Store |
| **Thumbprint** | `string` | Certificate Thumbprint of a certificate stored in the Windows Certificate Store. *(ProGet 2024.19+, BuildMaster 2024.1+, Otter 2024.1+ only)* |
| **Store** | `string` | The Windows Certificate Store that contains the certificate |
| **Location** | `CurrentUser/LocalMachine` | The Windows Certificate Store location |
| **AllowInvalid** | `true/false` | Whether to allow invalid certificates, like self-signed certificates, to be loaded |
| **IntegratedAuthenticationEnabled** | `true/false` | Enables integrated windows authentication *(ProGet 2024.30+ only)* |
| **UseHttpsRedirection** | `true/false` | When HTTPS is enabled, redirect HTTP port to HTTPS port *(ProGet 2024.30+ only)* |
| **EnableRequestLogging** | `true/false` | Enables HTTP request logging *(ProGet 2025.11+ only)* |
| **RequestLoggingDirectory** | `string` | The directory where request logs will be stored *(ProGet 2025.11+ only)* |

:::(Info)(Note:)
`Thumbprint` and `Subject` cannot be used at the same time.  If both are specified, only `Thumbprint` will be used.
:::

:::(Info)(Note:)
In ProGet 2024.30+, `IntegratedAuthenticationEnabled` and `UseHttpsRedirection` will be set automatically from the database configuration settings when [Enhanced Database Monitoring](/docs/installation/sql-server#enhanced-database-monitoring) is enabled.
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
