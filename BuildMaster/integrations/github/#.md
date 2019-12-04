---
title: GitHub
subtitle: Integrating BuildMaster with GitHub
sequence: 100 
keywords: buildmaster, github, git
show-headings-in-nav: true
---

GitHub is a hosting platform primarily for open-source projects, but also has support for issue tracking, release management, and collaboration. While GitHub does have its own version of a CI/CD platform in [GitHub Actions](https://github.com/features/actions), it is still in beta, and it also requires an organization's projects to be migrated to the GitHub platform in order to use it.

BuildMaster is designed to work *alongside* GitHub to provide a self-managed CI/CD solution.

Buildmaster's GitHub integration supports the following functionality:

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

## Connecting to and Authenticating GitHub with Resource Credentials {#authentication data-title="Authentication with GitHub"}
Authentication with GitHub is handled through [Resource Credentials](/docs/buildmaster/administration/resource-credentials). Each BuildMaster-supported SCM has its own type of Resource Credential, and that type is defined in the corresponding extension. In most cases, creating a resource credential using the GitHub type is sufficient, but you can also use Git for "generic" source control integration that doesn't rely on GitHub-specific concepts like "organizations" or GitHub releases. These credentials are effectively a username and password, so we recommend creating a specific account with the minimum amount of privileges required to interact with GitHub. Typically, this is just *Read* permission, but if there are requirements to add releases or tag source code, at least the *Write* permission will be required.

To connect to a self-managed instance of GitHub Enterprise, make sure the API URL of the resource credentials is configured to the hostname for your installation instead of GitHub's public API URL.

::: {.attention .technical}
SSH connections are not supported using the built-in GitHub integration in BuildMaster, so make sure to use the HTTPS endpoint when supplying the repository URL to the resource credentials or any operations. If your organization requires SSH to connect, you must install and configure the Git CLI manually and then instruct BuildMaster to use the [Git CLI instead](#cli).
:::

Resource credentials can be defined at the system-level or the application-level, and they can inherit from one another, such that configuring the "parent" resource credential will automatically give that configuration to its "child" credentials. This allows you to define some fields (such as name/password) at the system level, while defining repository-specific information at the application level.

## Source Control Integration {#source-control data-title="Source Control"}

BuildMaster's primary integration point with GitHub is source control, including pulling source code, tagging it, and capturing commit IDs to maintain association between build artifacts and specific commits.

### Working with GitHub Source Code

Different types of operations are used to get or check out source code from different SCM systems. 

#### Getting Source Code

To get source code from a repository, use the `GitHub::Get-Source` operation. This operation supports various optional levels of granularity including the branch name to pull from, the tag name, or even specific Git references such as the commit ID.

**Example: Get Source from a GitHub Repository**

This operation configuration has a lot of properties defined for illustrative purposes and will get source from the accounts repository under the kramerica-industries organization (no resource credential needed because it's a public):

GitHub::Get-Source
(
    Organization: kramerica-industries,
    Repository: accounts,
    DiskPath: c:\random-folder\accounts-$ReleaseNumber,
    Branch: rel-$ReleaseNumber,
    KeepInternals: true,
    CleanWorkspace: true,
    CommitHash => $CommitId,
);

**Using the Operations**

The specifics of these operations are documented in the corresponding Tool & Service Integrations pages, but they are all used similarly. Essentially, you specify various option using parameters:

 1. Specify a Resource Credential, which tells the operation which repository to use and how to connect. 
 2. Specify a Source Path or Branch, which tells the operation where to get the code from within the repository. This is repository-specific, as Git doesn't use source paths (you check out the whole repository).
 3. Specify Additional Options, such as whether to recurse submodules (Git-based repositories). Note: these are highly specific to each source control provider!
 4. Specify Target Directory to Get Code, which will default to the current working directory ($WorkingDirectory), but you may override as needed.

Like all operations in OtterScript, you specify variables for any of these parameters. 

**Git-based Operations Differences**

Git repositories are unique in that you can use the host-specific operation (such as GitLab::Get-Source) or the generic Git::GetSource operation. The primary difference is that you'll use project names with host-specific operations, and URLs in the generic operations.

In general, you should prefer Git host-specific operations, because it's less data to enter, and you may eventually move to a self-managed or private instance, which means you'll have to edit the URLs everywhere you entered them.

#### Tagging Source Code

"Tagging" or "labeling" source code is essentially a way to give a user-friendly name to the state of your source code at a specific point in time. Like the little flags marking where a gas line lies underground, these tags/labels help you find a specific code version among thousands or even millions of different versions.

Tags and labels can be used for all sorts of reasons, but they're most effective when used to identify which specific code was deployed to production. This makes it easy to:
 - Create a hotfix or patch for a deployed application
 - Give developers information about what code was deployed
 - Easily find and compare commits by tag/label name instead of obscure identifier

If you're already capturing the commit or revision number in BuildMaster, then each of these will already be possible. However, it's much easier for developers when this information in also source control: Not only is it faster to find information, but many source control systems will create branches or perform other operations directly from a label or tag.

To tag source code in a repository, use the `GitHub::Tag-Source`, which will assign a friendly name (such as `$ReleaseNumber.$BuildNumber`) to a specific commit. This is recommended during later stages of a pipeline to indicate which specific commits have actually been released, as opposed to ones leading up to the eventual release.
 
**Example: Tagging a Commit in GitHub**
This will tag a GitHub-hosted repository with the current release and build number:

GitHub::Tag(
    Credentials: Hdars-GitHub,
    Tag: $ReleaseName-$BuildNumber,
    CommitHash: $CommitId
);

By specifying $CommitId for the CommitHash parameter, this operation will likely not tag the current version of the code, but whatever version is represented by $CommitId. This could have been captured weeks prior.

#### Prompting for Branch Name before a Build

Even if you automatically create a new build each time code is checked-in, it's still important to have a manual process to create new builds. In either case, if you're often building from multiple branches, you can configure the branch name as a user prompt (with a drop-down of branches) that will appear when you create a new build an application.

Variable prompts are configured in an application's Release Templates. If you're not familiar with the feature, that's okay -- all applications have a "Default" release template that's used behind-the-scenes to determine which pipeline to use and which variables to prompt for at release, build, and deploy time.

To prompt for user input when creating a build, configure a [Release Template](/docs/buildmaster/releases/templates) for your application. The most common use-case for this is selecting a branch when building. This can be accomplished using a "GitHub Branches" dynamic list variable source as the type of build variable. After you configure a branch name variable prompt, BuildMaster will create a build-scoped variable based on whatever you select. You can use that variable in your OtterScript plans to get or checkout code from that branch.

**Example: Creating a Drop-down for GitHub Branches**
The following steps will configure a $BranchName variable prompt when new builds are created that defaults to master.

 - Navigate to the Default release template (Releases > Release Template > "Default")
 - Add a Build Template Variable
   - Type: Select "Dynamic List"
     - This will open a window where you can select the type of list
     - Select "GitHub Branches" as the type
     - Select the Credential you've already configured at the application
   - Name: BranchName
   - Initial value: master
   - Options: value is required

#### Built-in Git Client vs. `git` CLI {#cli data-title="Built-in Git Client vs. Git CLI"}

By default, BuildMaster does not require Git to be installed on a build server in order to perform source control operations. However, some users who require more advanced configuration for their Git integration may instruct BuildMaster to run the CLI instead by:

{.docs}
 - setting the `GitExePath` operation property to a valid Git executable path, e.g. `/usr/bin/git` on Linux or `C:\Program Files\Git\bin\git.exe` on Windows
 - configuring a `$DefaultGitExePath` variable at the server or system level in BuildMaster; this will force all Git source control operations to use the CLI instead of the built-in library

### Working with GitHub Releases

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

### Capturing Commit IDs

Git repositories use a a 20-byte SHA1 hashcode that represents the current state of the repository and identifies the commit. Capturing this reference at build time is very important so you can see exactly what code was used to build your application. 

Capturing this reference at build time is very important so you can see exactly what code was used to build your application.
 - **From a debugging perspective**, this saves you wasted time trying to figure out which code was deployed and saves frustration when the deployed application doesn't seem to match the code
 - **From an auditing perspective**, this lets you see exactly who made which changes to a deployed application and when those changes were made

BuildMaster gives you a lot of flexibility in capturing this information. Although you could always manually dig through the execution logs to find this information, a simpler approach is as follows.
 1. Specify an output parameter on the appropriate get or checkout operation; the operation will them set the value of runtime variable to be the commit number, revision number, etc.
 2. Set a build variable from the runtime variable; because runtime variables only exist during build time, capturing this as a build variable will create a permanent record on the build

In the GitHub::Get-Source operation, use the output property to capture into a variable (`CommitHash => $commitHash`), then use `Set-BuildVariable` to store it as `$CommitId`. Once this value is captured, you can use [variable value renderers](/docs/buildmaster/administration/variable-renderers) to link back to GitHub wherever the commit ID, most commonly on the overview page for a specific build.

**Example: Capturing CommitId from GitHub Repository**
By simply specifying the CommitHash output parameter and the $CommitId runtime variable, the Get-Source operation will set the value of $CommitId to be the commit hash of the code. You can then use this to identify the entire source code state:

GitHub::Get-Source
(
    Credential: GitHub,
    CommitHash => $CommitId,
);
Set-BuildVariable CommitId  
(  
    Value: $CommitId  
);

The second operation will then set $CommitId as a build variable, which means it will be not only visible on the build page, but you can use it in all future deployments on that build.

### Displaying CI Status on GitHub

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

Keeping clear track of issues is non-negotiable, and automating your issue tracker as much as possible helps avoid the frustrations and problems that come from tracking down all the people who had their hands on a release to see if it’s actually ready because no one remembered to update their issue notes. GitHub's issue tracking, while not as powerful a choice as other, private solutions, is an additional function of this tool -- and like so many other tools, GitHub's issue tracker integrates seamlessly with BuildMaster.

### Integrating with BuildMaster's Issue Tracking

A combination of Issue Sources and Resource Credentials are used to connect BuildMaster to GitHub's issue tracker. To associate issues, create a GitHub [Issue Source](/docs/buildmaster/verification/issue-tracking), which will link issues to releases in BuildMaster using the GitHub milestone.

**Example: Inedo Use Case**

On our [public instance of BuildMaster](https://buildmaster.inedo.com/), we have dozens of nearly identical applications configured that are designed to build/deploy the various extensions to our products. We’ve configured just a single issue source as follows: 
•	Name: GitHub Issue Source
•	Run for application/group: [Extensions](https://buildmaster.inedo.com/all-applications?applicationGroupId=1)
•	Credentials: InedoGitHub
•	Repository name: inedox-$ToLower($ApplicationName)
•	Milestone: $ReleaseNumber
This issue source will periodically synchronize with GitHub, and we’ve configured an automatic check that [prevents deployments until all issues are resolved].


### Creating Milestones

A milestone is the mechanism used to associate release numbers in BuildMaster with a specific GitHub issue and is intended to be a version number of sorts; it does not need to match the version used by marketing, but needs to be uniquely-identifiable so that BuildMaster can use it to apply gating logic in pipeline stages (i.e. all issues closed for *this* release). You can use the `GitHub::Ensure-Milestone` operation at an early stage to ensure that a milestone exists, or could be created at the end of a previous release.

### Closing Milestones

Closing a milestone in GitHub means the version represented by that milestone was "released" so no more issues should be associated with it. To close a milestone at the end of a pipeline, use the `GitHub::Ensure-Milestone` operation with the property: `State: closed`

### Creating and updating issues

To create issues for a particular GitHub milestone, use the `GitHub::Create-Issue` operation. To add a comment to an existing issue, use the `GitHub::Create-IssueComment` operation.
