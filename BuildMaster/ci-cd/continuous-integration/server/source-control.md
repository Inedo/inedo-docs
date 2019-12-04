---
title: Integrating Source Control Systems
sequence: 100
show-headings-in-nav: true
---

BuildMaster is designed to continuously deliver your applications and components from source code to production by integrating with the source control system(s) you are already using. Unlike monolithic CI/CD platforms like Azure DevOps or GitLab, BuildMaster works with any combination of source control tools or project hosting.

Source control integration in BuildMaster works a bit differently than dedicated Continuous Integration servers like Jenkins or TeamCity. With BuildMaster, you can:

 - Have one application use multiple source control systems
 - Let users select branches or use [custom branch selection](source-control/branching) logic
 - Check out source code at any point in the process
 - Tag/label code at any time, as needed
 - Easily change source control systems or hosts

All of this is relatively easy because most source control interactions are done within [OtterScript](/docs/otter/reference/otter-script).

## Connecting to Your Source Control System with Resource Credentials {#connecting data-title="Connecting to Source Control"}

[Resource credentials](/docs/buildmaster/administration/resource-credentials) are used to connect BuildMaster to your Source Control Management (SCM) system, whether that's a public repository on GitHub or a self-managed Subversion Server. Each supported SCM has its own type of Resource Credential, and that type is defined in the corresponding extension.

For example, installing the [Subversion extension](https://inedo.com/den/subversion) will allow you to create a Subversion Resource Credential. When creating a resource credential for a source control system, you'll enter two types of information:

- Repository information, such as the organization name in GitLab or custom endpoint for GitHub Enterprise
- Credential information, such as username and password or an authorization token 

Resource credentials can be defined at the system-level or the application-level, and they can inherit from one another, such that configuring the "parent" resource credential will automatically give that configuration to its "child" credentials. This allows you to define some fields (such as name/password) at the system level, while defining repository-specific information at the application level.


### Example: Inedo's Use Case

We use GitLab to host the source code for some of products. You're welcome to [request access](https://inedo.com/open/source-code-request) to it, by the way.

We have a system-level GitLab resource credential named 'GitLabInedo' that defines a group name (`inedo`), a username (`inedo-builds`), and personal access token (`hunter42`... just kidding). Each product with code hosted at GitLab (such as Otter or ProGet) has a corresponding application in BuildMaster, and those applications each have a credential named `GitLab` that inherits from `GitLabInedo` and defines a project name (`otter` or `proget`).

If we wanted to move to a self-managed GitLab instance, we'd just need to edit the `GitLabInedo` system-level credential, and all applications would still work.

### Variable and Resource Credentials 

Resource credentials also support [configuration variables](/docs/buildmaster/administration/configuration-variables) and [variable functions](/docs/buildmaster/reference/functions) for any of the fields. This is particularly useful when you've established identical naming conventions, such as ensuring your BuildMaster application name is the same as the source control repository's project name.

In our use case, we *could* have set the project name on the `GitLabInedo` credential to be `$ApplicationName`, but not all of our applications are named the same as the GitLab project, so it didn't make as much sense for us.

::: {.attention .best-practice} 
![Best Practice](/resources/images/icons/best-practices.png) 

**Best Practice**: The more convention-driven you make your applications (such as requiring the application be named the same as the source control repository), the more you should consider creating custom [application templates](/docs/buildmaster/administration/applications/application-templates) with [custom setup templates](/docs/buildmaster/administration/applications/setup-templates) to make it especially clear how to create new convention-driven applications.
:::


## Checking Out Your Source Code {#checking-out data-title="Checking Out Code"}

Different types of operations are used to get or check out source code from different SCM systems. For example, `Subversion::Svn-Checkout` is used with Subversion repositories and `TFS::Tfs-GetSource` is used for Team Foundation Version Control (TFVC).

### Example: Simple Checkout from a Subversion Repository

For example, here's a very basic checkout using Subversion and a credential named `KramericaSubversion`:

    Subversion::Svn-Checkout 
    (
	    Credential: KramericaSubversion,
	    SourcePath: trunk/hdars
    );

### Using the Operations 

The specifics of these operations are documented in the corresponding *Tool & Service Integrations* pages, but they are all used similarly. Essentially, you specify various option using parameters:

1. **Specify a Resource Credential**, which tells the operation which repository to use and how to connect. These can be specified and overridden on the operation itself, which means you technically don't need to use a resource credential at all. There are only a few cases where this would be appropriate, such as retrieving code from a public repository using its URL
2. **Specify a Source Path or Branch**, which tells the operation where to get the code from within the repository. This is repository-specific, as Git doesn't use source paths (you check out the whole repository), and Subversion's branches are specified via source path
3. **Specify Additional Options**, such as whether to use a clean a workspace each time (TFVS), [recurse submodules](https://git-scm.com/docs/gitsubmodules) (Git-based repositories), and so on. Note: these are highly specific to the source control provider
4. **Specify Target Directory to Get Code**, this will default to the current working directory (`$WorkingDirectory`), but you may override as needed

Like all operations in OtterScript, you specify variables for any of these parameters.

### Example: Get Source from a GitHub Repository 
This operation configuration has a lot of properties defined for illustrative purposes and will get source from the `accounts` repository under the `kramerica-industries` organization (no resource credential needed because it's a public):

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


## Git-based Operations Differences {#git-operation-differences data-title="Git-based Operations Differences"}

Git repositories are unique in that you can use the host-specific operation (such as `GitLab::Get-Source`) or the generic `Git::GetSource` operation. The primary difference is that you'll use project names with host-specific operations, and URLs in the generic operations.

In general, you should prefer Git host-specific operations, because it's less data to enter and you may eventually move to a self-managed or private instance, which means you'll have to edit the URLs everywhere you entered them.

## Capturing Commits and Revision Numbers {#capturing-commits data-title="Commits & Revision Numbers"}

Virtually all source control management systems have a unique identifier that can be used to reference the state of your source code at any given point in time. In Git repositories, this is a 20-byte SHA1 hashcode that represents the last committed changes; and in Subversion, this is an integer that increments every time a change is committed.

Capturing this reference at build time is very important so you can see *exactly* what code was used to build your application.

 - **From a debugging perspective**, this saves you wasted time trying to figure out which code was deployed and saves frustration when the deployed application doesn't seem to match the code
 - **From an auditing perspective**, this lets you see exactly who made which changes to a deployed application and when those changes were made

BuildMaster gives you a lot of flexibility in capturing this information. Although you could always manually dig through the execution logs to find this information, a simpler approach is as follows.

 1. Specify an *output parameter* on the appropriate get or checkout operation; the operation will them set the value of runtime variable to be the commit number, revision number, etc.
 2. Set a build variable from the runtime variable; because runtime variables only exist during build time, capturing this as a build variable will create a permanent record on the build


### Example: Capturing CommitId from GitHub Repository 
By simply specifying the `CommitHash` output parameter and the `$CommitId` runtime variable, the `Get-Source` operation will set the value of `$CommitId` to be the commit hash of the code. You can then use this to identify the entire source code state:

    GitHub::Get-Source
    (
	    Credential: GitHub,
	    CommitHash => $CommitId,
    );
	Set-BuildVariable CommitId  
    (  
	    Value: $CommitId  
    );

The second operation will then set `$CommitId` as a build variable, which means it will be not only visible on the build page, but you can use it in all future deployments on that build.

::: {.attention .best-practice } 
![Best Practice](/resources/images/icons/best-practices.png) 

**Best Practice: Use a Variable Renderer for User-friendly Display**
A 40-digit SHA1 code (i.e., a Git commit hash) is not at very user-friendly. Most web-based Git hosts will shorten these to eight characters for display purposes, while providing a hyperlink to view a page that shows the full commit.

You can do this in BuildMaster with [Variable Value Renderers](/docs/buildmaster/administration/value-renderers). These are essentially instructions for how to render variables in the UI that have certain names (such `$CommitId`). Because you can specify HTML in a value renderer, you link to your source control system while also providing a user-friendly code.
:::


## Tagging/Labeling Source Code {#tagging data-title="Tagging/Labeling Source Code"}
"Tagging" or "labeling" source code is essentially a way to give a user-friendly name to the state of your source code at a specific point in time. Like the little flags marking where a gas line lies underground, these tags/labels help you find a specific code version among thousands or even millions of different versions.

Tags and labels can be used for all sorts of reasons, but they're most effective when used to identify which specific code was deployed to production. This makes it easy to:

 - Create a hotfix or patch for a deployed application
  - Give developers information about what code was deployed
   - Easily find and compare commits by tag/label name instead of obscure
   identifier

If you're already [capturing the commit or revision number](#capturing-commits) in BuildMaster, then each of these will already be possible. However, it's much easier for developers when this information in also source control: Not only is it faster to find information, but many source control systems will create branches or perform other operations directly from a label or tag.

### Example: Tagging a Commit in GitHub 

This will tag a GitHub-hosted repository with the current release and build number:

    GitHub::Tag(
	    Credentials: Hdars-GitHub,
	    Tag: $ReleaseName-$BuildNumber,
	    CommitHash: $CommitId
    );

By specifying `$CommitId` for the `CommitHash` parameter, this operation will likely not tag the _*current*_ version of the code, but whatever version is represented by `$CommitId`. This could have been captured weeks prior.


::: {.attention .best-practice } 
![Best Practice](/resources/images/icons/best-practices.png) 

**Best Practice: Tag at the End of Your Release Pipelines**
Because a release-blocking defect can be discovered at any time prior to production, you can never be sure which specific build is deployed until after a build is deployed to production. Therefore, it's best to wait until after you've deployed to production to tag or label the code.
:::