---
title: Silent Installation
keywords: otter,installation
---

[Otter](/otter) helps you automate configuration and installation of *other* software on your servers, and to help you automate and configure Otter, the fully-automated silent installer can perform initial [installation](#silent-installation-options) and [upgrades](#silent-upgrade-options). You could even use *another* instance of Otter to upgrade Otter... and another instance to upgrade that one, until you've reached Inception.

See the [Otter Agent Silent Install Options](#silent-agent-install) for how to silently install agents as well; note that you will not need to upgrade agents, as they are automatically upgraded when you upgrade Otter.

### Silent Install Options {#silent-installation-options}

Option                                   | Description
-----------------------------------------|----------
/S                                       | **Required for a silent install.** If this is not specified, the UI will be displayed.
/Edition=&lt;value&gt;                   | **Required for a silent install.** The desired Otter edition: May be *Free*, *Trial*, or *LicenseKey*. {#edition-arg}
/EmailAddress=&lt;value&gt;              | The email address used for requesting a license key. Required if [Edition](#edition-arg)Edition is *Express* or *Trial*.
/FullName=&lt;value&gt;                  | The user's full name used for requesting a license key. Required if [Edition](#edition-arg) is *Express* or *Trial*.
/LicenseKey=&lt;value&gt;                | License key to use. Only used if [Edition](#edition-arg) is *LicenseKey*.
/TargetPath=&lt;value&gt;                | Root path where Otter will be installed. The default is *%ProgramFiles%*\\Otter. {#targetpath-arg}
/ASPNETTempPath=&lt;value&gt;            | Path where ASP.NET will store temporary files for the Otter web application. The default is *%ProgramData%*\\Otter\\Temporary ASP.NET Files.
/WebAppPath=&lt;value&gt;                | Path where the Otter web application is installed. The default is [[TargetPath](#targetpath-arg)]\\WebApp.
/ServicePath=&lt;value&gt;               | Path where the Otter service is installed. The default is [[TargetPath](#targetpath-arg)]\\Service.
/ExtensionsPath=&lt;value&gt;            | Path where the extension library is located. The default is *%ProgramData%*\\Extensions.
/ExtensionsTempPath=&lt;value&gt;        | Path where extensions are extracted and loaded from. The default is *%ProgramData%*\\ExtensionsTemp.
/ExecutionTempPath=&lt;value&gt;         | Path for the default working directory for executions using the local agent. The default is *%ProgramData%*\\Otter\\Executions.
/ConnectionString=&lt;value&gt;          | SQL Server connection string Otter will use to connect to the database. The default is *Data Source=localhost; Initial Catalog=Otter; Integrated Security=True;*.
/Port=&lt;value&gt;                      | Port number the Otter web application will listen to. The default is *81*. {#port-arg}
/UseIntegratedWebServer=&lt;value&gt;    | When *true*, the integrated web server is used to host Otter; when *false*, IIS is used. The default is *true*. {#useintegratedwebserver-arg}
/WebServerPrefixes=&lt;value&gt;         | Prefixes used by the integrated web server. The default is http://*:[[Port](#port-arg)]/.
/InstallSqlExpress                       | Installs a SQL Express instance named INEDO. The default is *false*.
/UserAccount=&lt;value&gt;               | User account of the Otter service and web application. The default is *NetworkService*. {#useraccount-arg}
/Password=&lt;value&gt;                  | Password of the user account of the Otter service and web application. Only used when [UserAccount](#useraccount-arg) is not one of the standard service accounts. {#password-arg}
/WebAppUserAccount=&lt;value&gt;         | User account of the Otter web application. The default is [[UserAccount](#useraccount-arg)].
/WebAppUserAccountPassword=&lt;value&gt; | Password of the user account of the Otter web application. The default is [[Password](#password-arg)].
/ServiceUserAccount=&lt;value&gt;        | User account of the Otter service. The default is [[UserAccount](#useraccount-arg)].
/ServiceUserAccountPassword=&lt;value&gt;| Password of the user account of the Otter service. The default is [[Password](#password-arg)].
/ConfigureIIS                            | Creates an IIS website and application pool to host the Otter web application. The default is *false*.
/LogFile=&lt;value&gt;                   | If specified, writes out a detailed log file of the installation process to the specified path value.

#### Example

Install Otter using the defalt settings with license key XXXXX:

```
OtterSetup.exe /S /LicenseKeys=XXXXX
```

### Silent Upgrade Options

Option                            | Description
----------------------------------|----------
/S                                | Required for a silent upgrade. If this is not specified, the UI will be displayed.
/Upgrade                          | Required to perform an upgrade. If this is not specified, the upgrade is not performed.
/BackupDatabase                   | Backs up the Otter database before the upgrade begins. The default is *true*.
/DatabaseBackupPath=&lt;value&gt; |  The path to save the backup of the Otter database. The default is *[SysDrive]:\\OtterBackups*.
/ConnectionString=&lt;value&gt;   | SQL connection string used for upgrading Otter's database schema. The default is whatever connection string the Otter service is currently using.
/LogFile=&lt;value&gt;            | If specified, writes out a detailed log file of the installation process to the specified path value.

#### Example

Upgrade Otter using the default settings:

```
OtterSetup.exe /S /Upgrade
```

### Agent Silent Install Options {#silent-agent-install}

/S | Required for a silent install. If this is not specified, the UI will be displayed.
/TargetPath=&lt;value&gt; | Root path where the agent will be installed. The default is *%ProgramFiles%*\\InedoAgent. {#targetpath-arg}
/Port=&lt;value&gt; | Port number the agent will listen to. The default is *46336*. {#port-arg}
/TempPath=&lt;value&gt; | Root path of the directory used by the agent for temporary files. The default is *%ProgramData%*\\InedoAgent. {#temppath-arg}
/OtterRootPath=&lt;value&gt; | Path where the Otter-specific Agent binaries will be stored. The default is [[TempPath](#temppath-arg)]\\Otter.
/UserAccount=&lt;value&gt; | User account of the agent. The default is *LocalSystem*. {#useraccount-arg}
/Password=&lt;value&gt; | Password of the user account of the agent. Only used when [UserAccount](#useraccount-arg) is not one of the standard service accounts. {#password-arg}
/Clients=&lt;value&gt; | Semicolon-separated list of client IP addresses which are allowed to connect to the agent. Any client may connect if this is not specified.
/ServiceName=&lt;value&gt; | The name of the Windows Service that will be installed. The default is *INEDOAGENTSVC*.
/Security=&lt;value&gt; | Either AES, SSL, or None. When SSL is specified, a valid [CertificateName](#certname-arg) must be specified. The default is AES.
/CertificateName=&lt;value&gt; | The friendly name of a certificate stored in the LocalMachine store.
/EncryptionKey=&lt;value&gt; | A 32-character hex string used for AES encryption.
/LogFile=&lt;value&gt; | If specified, writes out a detailed log file of the installation process to the specified path value.

#### Example

Install an agent with no encryption (bad idea, by the way) that listens on port 1000 (also not a great idea, because 46336 is the registered Inedo port):

```
OtterAgentSetup.exe /S /Security=None /Port=1000
```
