---
title: PowerShell
sequence: 150
keywords: proget,feeds,powershell
---
A PowerShell feed is a specialized type of [NuGet](nuget) feed intended to store PowerShell modules. It is accessible directly from [PowerShellGet](https://docs.microsoft.com/en-us/powershell/module/powershellget).

## Differences from NuGet Feeds

Since PowerShell module packages are actually NuGet packages, PowerShell feeds are functionally the same as NuGet feeds, except for some user-interface tweaks:

{.docs}
- PowerShell feed packages have their own icon to indicate that they are PowerShell modules
- Install instructions for a package display instructions for PsGet instead of NuGet
- Tags are not shown on the Browse Feed page because PowerShell packages use them differently, and tend to have large numbers of tags making them impractical to show in an overview

## Publishing to a PowerShell Feed

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
