---
title: "Subversion"
order: 8
---


Before Git, [Apache Subversion](https://subversion.apache.org/) was the industry standard for open-source source control systems. Although Git now dominates, Subversion (or SVN) is still very widely used for many open source projects by organizations around the world.

BuildMaster is designed to work with Subversion to provide a self-managed CI/CD solution and supports the following source control features:

| Operation | Description |
|---|---|
| Checkout  | Checks out a working copy from a repository |
| Update    | Bring changes from a repository into a working copy |
| Export    | Gets the unversioned contents of a repository to a specified directory |
| Copy      | Creates a copy of a source path to facilitate branching and tagging |
| Delete    | Deletes a file in a repository  |
	
::: (INFO)
**See it live!** See an example of Subversion integration by creating a new application using the *SVN CI/CD Sample*.
:::

## Installing the Subversion Extension 

Simply navigate to the *Admin* > *Extensions* page in your instance of BuildMaster and click on the Subversion extension to install it.

If your instance doesn't have Internet access, you can [manually install the Subversion extension](/docs/buildmaster/reference/extensions#manual-install) after downloading the [Subversion Extension Package](https://proget.inedo.com/feeds/Extensions/inedox/Subversion).

## Authenticating to a Subversion Repository 

The simplest and recommended method to connect to a Subversion repository is to create a "Subversion Repository" [Secure Resource](/docs/buildmaster/configuring-for-your-team/buildmaster-administration-resource-credentials). If your repository requires authentication (which most do), you can associate this secure resource with a User name & Password [Secure Credential](/docs/buildmaster/configuring-for-your-team/buildmaster-administration-resource-credentials).

Although every SVN operation supports specifying a repository URL, username, and password, we recommend creating a resource credential that includes the repository name, username, and password. This is not only more secure, but also more convenient as the credentials are stored in one place.

::: (ERROR)
SSH connections are not supported by the built-in Subversion integration in BuildMaster. Therefore, make sure you use the HTTPS endpoint when passing the repository URL to the resource credentials or other operations. If your organization requires SSH for connection, you must manually install and configure SVN CLI and then instruct BuildMaster to use [SVN CLI instead](#cli).
:::

## Source Control Integration 

Subversion functions both for source control and continuous integration, and BuildMaster supports both. The most common use-case when working with Subversion is to get the latest source code (e.g., `trunk`) then build and create a build artifact. 

### Checking Out the Latest Source Code from a Subversion Repository

To check out the latest source code from SVN, you can use the `Svn-Checkout` operation within your script:

```
Svn-Checkout(
    RepositoryUrl: https://github.com/inedo/inedox-subversion.git,
    SourcePath: trunk,
    To: ~\Sources
);
```

Note that, for illustrative purposes, this example is *not* using a [Subversion Repository Secure Resource](#authentication) as we recommended.

This operation effectively runs the equivalent SVN command: 

```
svn.eve checkout https://github.com/inedo/inedox-subversion.git
```

*Note: the `.git` extension works in this case because GitHub also supports Subversion clients.*

### Checking Out Branches in Subversion

In Subversion, branches are simply separate paths in the source tree. Many organizations use branches to determine the specific code that will be built for a given release. Branches allow teams to work simultaneously on the same project without overwriting another team member's work or presenting risks to the source code.

In BuildMaster, checking out a branch is as easy as updating the `SourcePath`:

```
Svn-Checkout(
    RepositoryUrl: https://github.com/inedo/inedox-subversion.git,
    SourcePath: branches/v1.2.5,
    To: ~\Sources
);
```

A more general approach is using BuildMaster functions (e.g. `SourcePath: branches/$ReleaseNumber`), configuration variables, or even [release template variable prompts](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-applications-releases-templates#components) that enable the branch to be selected at build time.

### Branching and Tagging in Subversion

As mentioned earlier, branches are simply separate paths in the source code. Branches can be created automatically at any stage of a pipeline, for example, to create a release branch for the next release in your application cycle.

To create a branch in Subversion, use the `Svn-Copy` operation:

```
Svn-Copy(
    RepositoryUrl: https://github.com/inedo/inedox-subversion.git,
    From: trunk,
    To: branches/v$ReleaseNumber
);
```

This operation executes the equivalent SVN command when run under the context of release 1.2.5:

```
svn.exe copy trunk branches/v1.2.5
```

Similarly, tagging in Subversion follows the same process of copying source paths; in SVN, tagging and branching work the same way (i.e., copying a path). Tags can be thought of as "read-only snapshots" of the code in a repository at a particular point in time, where the tag is a descriptive identifier (e.g. `Release-1.2.5`).

To create a tag in Subversion, use the `Svn-Copy` operation:

```
Svn-Copy(
    RepositoryUrl: https://github.com/inedo/inedox-subversion.git,
    From: trunk,
    To: tags/Release-$ReleaseNumber
);
```

This operation runs the equivalent SVN command:

```
svn.exe copy trunk tags/Release-1.2.5
```

::: (INFO) 
Because the branching/tagging operations require write access, they will likely require [authentication](#authentication) with the repository.
:::

## Continuous Integration 

Another use case for SVN is CI, and BuildMaster's Repository Monitor integrates seamlessly with SVN for continuous integration.

### Automated Builds

BuildMaster supports automatic monitoring of a Subversion repository for changes, regardless of where it is hosted. BuildMaster simply checks SVN periodically to see if new revisions have appeared, and if new changes are found, BuildMaster will automatically create a new build in BuildMaster.

To automatically create builds when developers commit to a Subversion repository, simply configure a [resource monitor](/docs/buildmaster/administration/buildmaster-resource-monitors).

::: (ERROR)
Note: A Subversion repository monitor requires BuildMaster v6.1 or later in combination with v1.1.0 or later of the Subversion extension.
:::

#### Available Variables

:::(Internal) (NOT IMPLEMENTED)
Unfortunately, this is no longer the case. This will be stopped in BuildMaster 2022.
:::

When using a [repository monitor script](/docs/buildmaster/administration/buildmaster-resource-monitors), the following variables are available:

 - `$Branch` - the full path of the branch, e.g. `branches/develop-1.2.3`
 - `$RevisionNumber` - the highest integer revision number of any file within the specified path

#### Built-in Subversion Client vs. `svn` CLI 

By default, BuildMaster does not require SVN to be installed on a build server to perform source control operations. However, some users who require advanced configuration for their SVN integration can instruct BuildMaster to run CLI instead:

 - set the 'SvnExePath' operation property to a valid SVN execution path, e.g. '/usr/bin/svn' on Linux or 'C:\Program Files\Subversion\bin\svn.exe' on Windows
 - configure a '$SvnExePath' variable at server or system level in BuildMaster; this will force all Subversion source control operations to use the CLI instead of the built-in library