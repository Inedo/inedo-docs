---
title: "Silent Installation"
order: 3
---

The Inedo Agent installer can also be run without displaying a window, which is ideal for Infrastructure as Code or script-based installations. To perform a silent installation, simply run the executable file from the command line or PowerShell with the `/S` argument.

#### Argument Format:

Silent installation arguments follow the format:

```
InedoAgentSetup.«version».exe /«argument-name»="«argument-value»"
```

_Note: quotes are required if the `argument-value` contains whitespace._

#### Example:

    .\InedoAgentSetup.42.exe /S /TargetPath="C:\Program Files\InedoAgent" /Port=46336 /TempPath="C:\ProgramData\InedoAgent" /ServiceName="INEDOAGENTSVC" /Security=AES /EncryptionKey="0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef"

#### Available Arguments

|Name|Default Value|Notes|
|--- |--- |--- |
|S|*(not applicable)*|This argument instructs the installer not to display a window, and does not have an `argument-value`|
|TargetPath|%ProgramFiles%\InedoAgent|The directory that will contain the service's executable and supporting files. If specified, `argument-value` must be an absolute path (i.e. %PROGRAMFILES will not expand)|
|Port|46336|
|TempPath|%ProgramData%\InedoAgent|The base directory for any *Product*RootPath option not explicitly specified. If specified, `argument-value` must be an absolute path (i.e. %PROGRAMDATA will not expand)|
|UserAccount|LocalSystem|The user account to run the service under. For built-in accounts, use `NetworkService` or `LocalSystem`|
|Password||The password for UserAccount. Omit this field for built-in accounts.|
|ServiceName|INEDOAGENTSVC|
|Security|AES|Also accepts SSL or None. It is recommended that this is left as AES as the other options do not have any way of validating the identity of the client.|
|CertificateName||Used if Security is set to SSL. Represents the case-sensitive friendly name of a certificate in the Local Machine store.|
|EncryptionKey||Used if Security is set to AES. Must be a 64-character hex string. If EncryptionKey is not specified, one will be randomly generated (which could overwrite an existing key on upgrade).|