---
title: Source Control
subtitle: Azure DevOps Source Control Integration
sequence: 100 
keywords: buildmaster, git, azure-devops, source-control
show-headings-in-nav: true
---

BuildMaster's primary integration point with Azure DevOps is source control stored in Azure Repos. This includes pulling source code, tagging it, and capturing commit IDs to maintain association between build artifacts and specific commits. [[ATTN: JOHN - value of this? Why do we want to maintain the association?]]

## Checking Out Your Source Code {#checking-out data-title="Checking Out Source Code"}

To get source code, use the `AzureDevOps::Get-Source` operation in a build or deployment plan. This operation supports various optional levels of granularity including the branch name to pull from, the tag name, or even specific Git references such as the commit ID.

An example of using the `AzureDevOps::Get-Source` operation is:

```
AzureDevOps::Get-Source
(
    Organization: inedo,
    Repository: ProfitCalc,
    CommitHash => $CommitId
);
```

### Git-based Operations Differences {#operations-differences}

Git source repositories are unique in that you can use either a host-specific operation (in this case, `AzureDevOps::Get-Source`) or the generic `Git::GetSource` operation. The primary difference is that you'll use project names for Azure DevOps operations and URLs in the generic operations.

In general, you should prefer Azure DevOps-specific operations, because it's less data to enter and you may eventually move to a self-managed or private instance, which means you'll have to edit the URLs everywhere you entered them.

## Built-in Git Client vs. Git CLI {#git-cli}  [[ATTN: JOHN - is this supposed to be Git??]]

By default, BuildMaster does not require Git to be installed on a build server in order to perform source control operations. However, some users who require more advanced configuration for their Git integration may instruct BuildMaster to run the CLI instead by:

{.docs}
 - setting the `GitExePath` operation property to a valid Git executable path, e.g. `/usr/bin/git` on Linux or `C:\Program Files\Git\bin\git.exe` on Windows
 - configuring a `$DefaultGitExePath` variable at the server or system level in BuildMaster; this will force all Git source control operations to use the CLI instead of the built-in library

## Capturing Commit IDs {#capturing data-title="Capturing Commit IDs"} [[ATTN: JOHN - is this supposed to be Git??]]

Git repositories reference specific points in the history of a source tree using a 20-byte SHA1 hashcode. At the tip of a branch, this hash effectively represents the last committed changes. Capturing this reference at build time is very important so you can see exactly what code was used when an application was built:

{.docs}
 - **From a debugging perspective**, this saves you wasted time trying to figure out which code was deployed and saves frustration when the deployed application doesn't seem to match the code
 - **From an auditing perspective**, this lets you see exactly who made which changes to a deployed application and when those changes were made

BuildMaster gives you a lot of flexibility in capturing this information. Although you could always manually dig through the execution logs to find this information, a simpler approach is:

 1. Specify an *output* parameter on the appropriate get or checkout operation; the operation will then set the value of runtime variable to be the commit number, revision number, etc.
 2. Set a build variable from the runtime variable; because runtime variables only exist during build time, capturing this as a build variable will create a permanent record on the build

### Example: Capturing CommitId from an Azure DevOps Repository

By simply specifying the `CommitHash` output parameter and the `$CommitId` runtime variable, the `AzureDevOps::Get-Source` operation will set the value of `$CommitId` to be the commit hash of the code. You can then use this to identify the entire source code state:

```
AzureDevOps::Get-Source
(
    Credential: AzureDevOps,
    CommitHash => $CommitId,
);
Set-BuildVariable CommitId  
(  
    Value: $CommitId  
);
```

The second operation will then set `$CommitId` as a build variable, which means it will be not only visible on the build page, but you can use it in all future deployments on that build.

### Best Practice: Use a Variable Renderer for User-friendly Display

A 40-digit SHA1 code (i.e., a Git commit hash) is not at very user-friendly. Most web-based Git hosts will shorten these to eight characters for display purposes, while providing a hyperlink to view a page that shows the full commit.

You can do this in BuildMaster using a [variable value renderer](/docs/buildmaster/administration/value-renderers). These are essentially instructions for how to render variables in the UI that have certain names (such `$CommitId`). Because you can specify HTML in a value renderer, you link to your source control system while also providing a user-friendly code.

## Tagging Source Code {#tagging data-title="Tagging Source Code"}

"Tagging" source code is essentially a way to give a user-friendly name to the state of your source code at a specific point in time. Like the little flags marking where a gas line lies underground, these tags help you find a specific code version among thousands or even millions of different versions.

Tags and labels can be used for all sorts of reasons, but they're most effective when used to identify which specific code was deployed to production. This makes it easy to:

{.docs}
 - Create a hotfix or patch for a deployed application
 - Give developers information about what code was deployed
 - Easily find and compare commits by tag/label name instead of obscure identifier

If you're already capturing the commit ID in BuildMaster, each of these will already be possible. However, it's much easier for developers when this information in also source control: Not only is it faster to find information, but many source control systems will create branches or perform other operations directly from a label or tag. [[ATTN: JOHN - can we make the part of the sentence about "many source control systems" instead be about ADO? Like "but AzureDevOps will do XYZ" ??]]

To tag source code in a repository, use the `AzureDevOps::Tag`, which will assign a friendly name (such as `$ReleaseNumber.$BuildNumber`) to a specific commit. This is recommended during later stages of a pipeline to indicate which specific commits have actually been released, as opposed to ones leading up to the eventual release.

### Example: Tagging a Commit in Azure DevOps

This will tag a Azure DevOps-hosted repository with the current release and build number:

```
Azure DevOps::Tag
(
    Credentials: AzureDevOps,
    Tag: $ReleaseName-$BuildNumber,
    CommitHash: $CommitId
);
```

By specifying `$CommitId` for the `CommitHash` parameter, this operation will likely not tag the current version of the code, but whatever version is represented by `$CommitId`. This could have been captured weeks prior.

### Best Practice: Tag at the End of Your Release Pipelines

Because a release-blocking defect can be discovered at any time prior to production, you can never be sure which specific build is deployed until after a build is deployed to production. Therefore, it's best to wait until after you've deployed to production to tag or label the code.
