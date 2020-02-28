---
title: PowerShell
sequence: 150
keywords: proget,feeds,powershell
show-headings-in-nav: true
---
A PowerShell feed is a specialized type of [NuGet](nuget) feed intended to store PowerShell modules. It is accessible directly from [PowerShellGet](https://docs.microsoft.com/en-us/powershell/module/powershellget).

## Installing Modules {#installing data-title="Installing Modules"}

PowerShell packages are installed using [PowerShell](https://docs.microsoft.com/en-us/powershell/module/PowerShellGet/Install-Module). To install a package from a ProGet feed, use the following commands:

### 1. Register Module
```
PS> Register-PSRepository -Name "{feed-name}" -SourceLocation "http://{proget-server}/nuget/{feed-name}/"
```

### 2. Install Module

```
PS> Install-Module -Name "{package-name}" -RequiredVersion "{package-version}" -Repository "{feed-name}"
```


## Differences from NuGet Feeds {#differences data-title="Differences from NuGet Feeds"}

Since PowerShell module packages are actually NuGet packages, PowerShell feeds are functionally the same as NuGet feeds, except for some user-interface tweaks:

{.docs}
- PowerShell feed packages have their own icon to indicate that they are PowerShell modules
- Install instructions for a package display instructions for PsGet instead of NuGet
- Tags are not shown on the Browse Feed page because PowerShell packages use them differently, and tend to have large numbers of tags making them impractical to show in an overview

## PowerShell Module Package Version Quirks {#versioning-quirks data-title="Versioning Quirks"}

Due to a validation bug in the Microsoft-run PowerShell Gallery, several packages have two different version numbers: one in the .nuspec file, and one in the .psd1 of the module. 

This causes a lot of problems, the most common is that when a package is installed from a ProGet repository onto a Windows machine, it ends up in the "wrong" location. For example, SqlServerDsc will install to `C:\Program Files\WindowsPowerShell\Modules\SqlServerDsc\12.2.0` while the .psd1 actually says 12.2.0.0. As a result, PowerShell refuses to load the module because it believes the manifest is incorrect.

One workaround is to turned off caching for the PSGallery connector, and then go through and remove the incorrectly versioned packages  (pull them to proget first, then delete them). After that, ProGet can now delivering packages directly from the connector with the correct version number.

As of 2019-Apr-08, this is being tracked in [PowerShellGallery Issue #55](https://github.com/PowerShell/PowerShellGallery/issues/55) and [work-arounds discussed on our Q&A site](https://inedo.com/support/questions/9738).

### PowerShell Missing Packages {#missing-packages data-title="Missing Packages"}

Due to another bug in the Microsoft-run PowerShell Gallery, their API will report that some packages don't exist. The PowerShell client attempts to workaround this bug by attempting to query again, using a different queries, but it only attepts that workaround when querying PowerShell Gallery. It will not attempt that workaround when querying ProGet.

The easiest workaround for this bug is to simply pull these packages locally. 

As of 2018-Oct-01, this being tracked in [PowerShellGallery Issue #30](https://github.com/PowerShell/PowerShellGallery/issues/30).

## Publishing to a PowerShell Feed {#publishing data-title="Publishing to a PowerShell Feed"}

You can register and publish to a PowerShell feed in ProGet easily using PowerShell 5.0 or newer and [PowerShellGet](https://docs.microsoft.com/en-us/powershell/module/powershellget).

`Import-Module PowerShellGet`

```
  Register-PSRepository -Name MyProGetFeed -SourceLocation <ProGetUrl> /nuget/<FeedName>/ -PublishLocation <ProGetUrl>/nuget/<FeedName>/
Publish-Module -Path <ModuleFile> -NuGetApiKey <UserName>:<Password>-Repository MyProGetFeed
```
If you would prefer to use an API key instead of user/password credentials, you can create a [Feed API Key](../administration/security/api-keys) in ProGet and use the following format for `Publish-Module` instead:

```
Publish-Module -Path <ModuleFile> -NuGetApiKey <API Key> -Repository MyProGetFeed
```
