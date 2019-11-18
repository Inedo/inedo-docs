---
title: GitHub
subtitle: Integrating BuildMaster with GitHub
sequence: 100 
keywords: buildmaster, github, git
show-headings-in-nav: true
---

GitHub is a hosting platform primarily for open-source projects, but also has support for issue tracking, release management, and collaboration. While GitHub does have its own version of a CI/CD platform in [GitHub Actions](https://github.com/features/actions), it is still in beta, and also requires an organization's projects to be migrated to the GitHub platform in order to use it.

BuildMaster is designed to work alongside GitHub to provide a self-managed CI/CD solution and supports the following functionality:

{.docs}
 - [Source Control](/docs/buildmaster/builds/continuous-integration/source-control)
 - [Continuous Integration](/docs/buildmaster/builds/continuous-integration)
 - [Issue Tracking](/docs/buildmaster/verification/issue-tracking)
	
::: {.attention .best-practice}
**See it live!** See an example of GitHub integration by creating a new application using the *GitHub CI/CD* template.
:::

## Installing the GitHub Extension {#extension data-title="GitHub Extension"}

Simply navigate to the *Admin* > *Extensions* page in your instance of BuildMaster and click on the GitHub extension to install it.

If your instance doesn't have internet access, you can [manually install the GitHub extension](https://docs.inedo.com/docs/buildmaster/reference/extensions#manual-install) after downloading the [GitHub Extension Package](https://proget.inedo.com/feeds/Extensions/inedox/GitHub).

## Authentication with GitHub {#authentication data-title="Authentication with GitHub"}

Authentication with GitHub is handled through [Resource Credentials](/docs/buildmaster/administration/resource-credentials). In most cases, creating a resource credential using the GitHub type is sufficient, but you can also use Git for "generic" source control integration that doesn't rely on GitHub-specific concepts like "organizations" or GitHub releases. These credentials are effectively a username and password, so we recommend creating a specific account with the minimum amount of privileges required to interact with GitHub, typically this is just *Read* permission, but if there are requirements to add releases or tag source code, at least the *Write* permission will be required. 

To connect to a self-managed instance of GitHub Enterprise, make sure the API URL of the resource credentials is configured to the hostname for your installation instead of GitHub's public API URL.

::: {.attention .technical}
SSH connections are not supported using the built-in GitHub integration in BuildMaster, so make sure to use the HTTPS endpoint when supplying the repository URL to the resource credentials or any operations. If your organization requires SSH to connect, you must install and configure the Git CLI manually and then instruct BuildMaster to use the [Git CLI instead](#cli).
:::

## Source Control Integration {#source-control data-title="Source Control"}

BuildMaster's primary integration point with GitHub is source control, including pulling source code, tagging it, and capturing commit IDs to maintain association between build artifacts and specific commits.

### Working with GitHub Source Code

#### Getting source code

To get source code from a repository, use the `GitHub::Get-Source` operation. This operation supports various optional levels of granularity including the branch name to pull from, the tag name, or even specific Git references such as the commit ID.

#### Tagging source code

 To tag source code in a repository, use the `GitHub::Tag-Source`, which will assign a friendly name (such as `$ReleaseNumber.$BuildNumber`) to a specific commit. This is recommended during later stages of a pipeline to indicate which specific commits have actually been released, as opposed to ones leading up to the eventual release.
 
#### Prompting for branch name before a build

To prompt for user input when creating a build, configure a [Release Template](/docs/buildmaster/releases/templates) for your application. The most common use-case for this is selecting a branch when building. This can be accomplished using a "GitHub Branches" dynamic list variable source as the type of build variable.

#### Built-in Git Client vs. `git` CLI {#cli data-title="Built-in Git Client vs. Git CLI"}

By default, BuildMaster does not require Git to be installed on a build server in order to perform source control operations. However, some users who require more advanced configuration for their Git integration may instruct BuildMaster to run the CLI instead by:

{.docs}
 - setting the `GitExePath` operation property to a valid Git executable path, e.g. `/usr/bin/git` on Linux or `C:\Program Files\Git\bin\git.exe` on Windows
 - configuring a `$DefaultGitExePath` variable at the server or system level in BuildMaster; this will force all Git source control operations to use the CLI instead of the built-in library

### Working with GitHub releases

GitHub releases are essentially tagged released (or beta) versions of a program, installer, container, or other packaged artifact. They appear in the "Releases" tab of a specific GitHub project, and are the most common way of publishing an executable for GitHub-hosted projects.

#### Creating GitHub releases

To create a GitHub release, use the `GitHub::Ensure-Release` operation. This operation will effectively create a release (or update one that already exists). Once a release exists, use the `GitHub::Upload-ReleaseAssets` operation to upload files (i.e. binaries, build output) to the release so that users can download artifacts from the releases tab in GitHub. 

If the release isn't quite ready for production, you can use the `PreRelease` option to create the release as a "beta" first, upload assets to it, then update the release to stable in a later pipeline stage if desired by amending the properties of the `GitHub::Ensure-Release` operation in that stage.

It's important to note that release assets are not designed to replace artifact libraries, they are more a target of deployment than a storage mechanism for libraries like [ProGet](https://inedo.com/proget).

## Continuous Integration {#ci data-title="Continuous Integration"}

### Automated Builds

Automated builds are the process of creating builds at certain intervals or in response to commits and pushes to a repository. For GitHub, they can be achieved in multiple ways:

{.docs}
 - scheduled to create a build at a specified frequency (hourly, daily, etc.)
 - create a build as a result of a webhook monitor (see https://github.com/Inedo/inedox-git/wiki/configuring-github-hooks for how to configure this)
 - poll for commits and create a new build using a [respository monitor](/docs/buildmaster/builds/continuous-integration/build-triggers-and-monitors/repository-monitors), useful if BuildMaster is firewalled off from the internet

### Capturing commit IDs

Git repositories use a a 20-byte SHA1 hashcode that represents the current state of the repository and identifies the commit. Capturing this reference at build time is very important so you can see exactly what code was used to build your application. 

In the GitHub::Get-Source operation, use the output property to capture into a variable (`CommitHash => $commitHash`), then use `Set-BuildVariable` to store it as `$CommitId`. Once this value is captured, you can use [variable value renderers](/docs/buildmaster/administration/variable-renderers) to link back to GitHub wherever the commit ID, most commonly on the overview page for a specific build.

### Displaying CI status on GitHub

BuildMaster can set GitHub CI status using the `GitHub::Set-Status` operation which operates on a specific commit. The main use-case is to display an icon on GitHub to display the build status for BuildMaster build of the commit, and statuses can be set to:

{.docs}
 - **auto** - BuildMaster automatically chooses the status
 - **pending** - a build is about to start or is running
 - **success** - build completed successfully
 - **failure** - execution didn't complete or there was an error unrelated to build compilation/tests
 - **error** - execution completed but with a build error 
 
An example plan that uses this operation is as follows:

```
try
{
	GitHub::Set-Status 
	(
		CommitHash: $CommitId,
        Status: pending
    );
	
	# other build operations...
}
finally
{
	GitHub::Set-Status 
	(
		CommitHash: $CommitId,
       Status: auto
    );
}
```

### Displaying a CI badge in a GitHub README

Refer to the [CI Badges](/docs/buildmaster/builds/continuous-integration/badges) documentation for examples on how to render a CI badge on a GitHub readme file.

## Issue Tracking {#issues data-title="Issue Tracking"}

### Integrating with BuildMaster's Issue Tracking

A combination of Issue Sources and resource credentials are used to connect BuildMaster to GitHub's issue tracker. To associate issues, create a GitHub [Issue Source](/docs/buildmaster/verification/issue-tracking), which will link issues to releases in BuildMaster using the GitHub milestone.

### Creating milestones

A milestone is the mechanism used to associate release numbers in BuildMaster with a specific GitHub issue and is intended to be a version number of sorts; it does not need to match the version used by marketing, but needs to be uniquely-identifiable so that BuildMaster can use it to apply gating logic in pipeline stages (i.e. all issues closed for *this* release). You can use the `GitHub::Ensure-Milestone` operation at an early stage to ensure that a milestone exists, or could be created at the end of a previous release.

### Closing milestones

Closing a milestone in GitHub means the version represented by that milestone was "released" so no more issues should be associated with it. To close a milestone at the end of a pipeline, use the `GitHub::Ensure-Milestone` operation with the property: `State: closed`

### Creating and updating issues

To create issues for a particular GitHub milestone, use the `GitHub::Create-Issue` operation. To add a comment to an existing issue, use the `GitHub::Create-IssueComment` operation.
