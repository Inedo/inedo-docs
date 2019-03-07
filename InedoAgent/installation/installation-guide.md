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

### 2. Download Otter Windows Agent {#download data-title="2. Download Otter Windows Agent"}

When you click the "Create Server" button on the Servers page in your Otter or BuildMaster installation, you'll be given a direct link to the file with the correct version. You can also find it on the [all Otter downloads](/otter/versions) page or the [all BuildMaster downloads](/buildmaster/versions) page. Make sure to select the version that corresponds with your version of Otter or BuildMaster, which can be found in the bottom-right corner in your web browser.

### 3. Target Directory {#target data-title="3. Target Directory"}

Although you may install wherever you like, it is recommended to keep the temporary directory short to avoid potential issues with tools that have problems with long paths.

### 4. User Account {#user data-title= "4. User Account"}

By default, the agent service will run as [local system](https://msdn.microsoft.com/en-us/library/windows/desktop/ms677973), which is an administrative service account. While not required, administrator-level privileges are strongly recommended as it will eliminate any difficulty or confusion with moving files and running different operations.

### 5. Network {#network data-title= "5. Network"}

We recommend using the default port (46336), as that's Inedo's [officially designated port](http://iana.org/assignments/port-numbers) Make sure that the selected port is not blocked by a firewall.

You can change this later by editing the configuration file in the installation directory, and restarting the service.

### 6. Encryption {#encryption data-title= "6. Encryption"}

Inedo's agents use a FIPS-compliant symmetric AES encryption scheme by default, and the installer will
automatically generate a secure, 32-digit key for you. You can use that key, or another one of your choosing.

If you select SSL instead of built-in AES, then you will need to have a valid certificate installed on the server (see [KB#1040](/support/kb/1040")).

**No Encryption** is also an option, but we don't recommend using it. The primary reason we have this is so that you can use a tool like [Wireshark](https://www.wireshark.org/) to inspect individual messages, either as directed by our support engineers or because you really like studying TCP protocols.{.announcement}

You can change this later by editing the configuration file in the installation directory, and restarting the service.

### 7. Summary {#summary data-title= "7. Summary"}

Verify that the information here is correct and then click Install.

### 8. Installation {#installation data-title= "8. Installation"}

The actual installation is near instantaneous, and should take no more than a minute or two, even on the slowest of systems.

Once the installation is complete, just close the installer or click the Exit button.

## Installing Multiple Agents on Same Machine {#multiple data-title= "Installing Multiple Agents on Same Machine"}

Sometimes, it may be desired to install multiple instances of the Inedo Agent on the same machine. The most common reason for this is privileges; one agent may run in a higher privilege mode than the other.

The automated and manual agent installer do not currently support this, but [KB#1146](/support/kb/1146) will provide guidance on how to do this.

## Silent Install {#silent data-title="Silent Install"}
**Name** | **Default Value** | **Notes**
---------- | ----------- | ----------
TargetPath | %ProgramFiles%\InedoAgent | The directory that will contain the service's executable and supporting files.
Port | 46336 |
TempPath | %ProgramData%\InedoAgent | The base directory for any *Product* RootPath option not explicitly specified.
OtterRootPath | *TempPath*\Otter |
BuildMasterRootPath | *TempPath*\BuildMaster |
HedgehogRootPath | *TempPath*\Hedgehog |
UserAccount | LocalSystem | The user account to run the service under.
Password | | The password for UserAccount
ServiceName | INEDOAGENTSVC |
Security | AES | Also accepts SSL or None. It is recommended that this is left as AES as the other options do not have any way of validating the identity of the client.
CertificateName | | Used if Security is set to SSL.
EncryptionKey | | Used if Security is set to AES. Must be a 32-character hex string. If EncryptionKey is not specified, one will be randomly generated.
