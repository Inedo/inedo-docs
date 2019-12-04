---
title: Subversion
subtitle: Integrating BuildMaster with Subversion (SVN)
sequence: 400 
keywords: buildmaster, source-control, subversion
show-headings-in-nav: true
---

Before the introduction of Git, [Apache Subversion](https://subversion.apache.org/) was the industry standard for open-source source control systems. It is still however very commonly used for many open-source projects and throughout organizations around the world.

BuildMaster is designed to work with Subversion to provide a self-managed CI/CD solution and supports the following source control functionality:

| Operation | Description |
|---|---|
| Checkout  | Checks out a working copy from a repository |
| Update    | Bring changes from a repository into a working copy |
| Export    | Gets the unversioned contents of a repository to a specified directory |
| Copy      | Creates a copy of a source path to facilitate branching and tagging |
| Delete    | Deletes a file in a repository  |
	
::: {.attention .best-practice}
**See it live!** See an example of Subversion integration by creating a new application using the *SVN CI/CD* template.
:::

## Installing the Subversion Extension {#extension data-title="Subversion Extension"}

Simply navigate to the *Admin* > *Extensions* page in your instance of BuildMaster and click on the Subversion extension to install it.

If your instance doesn't have internet access, you can [manually install the Subversion extension](https://docs.inedo.com/docs/buildmaster/reference/extensions#manual-install) after downloading the [Subversion Extension Package](https://proget.inedo.com/feeds/Extensions/inedox/Subversion).

## Authenticating to a Subversion Repository {#authentication data-title="Authentication with SVN"}

Authentication with Subversion is handled through [Resource Credentials](/docs/buildmaster/administration/resource-credentials), which supports username/password authentication over HTTPS. This is the simplest and recommended method to authenticate with a Subversion repository. While each SVN operation supports supplying a repository URL, username, and password, it is recommended to create a resource rredential that includes the repository name, username, and password. This is not only more secure, but more convenient as the credentials are stored in one location.

::: {.attention .technical}
SSH connections are not supported using the built-in Subversion integration in BuildMaster, so make sure to use the HTTPS endpoint when supplying the repository URL to the resource credentials or any operations. If your organization requires SSH to connect, you must install and configure the SVN CLI manually and then instruct BuildMaster to use the [SVN CLI instead](#cli).
:::

## Source Control Integration {#source-control data-title="Source Control"}

### Checking out the Latest Source Code from a Subversion Repository

The most common use-case when working with Subversion is getting the latest source code (e.g. `trunk`), then building it and creating a build artifact. 

To do this, you can use the `Svn-Checkout` operation within your plan:

```
Svn-Checkout(
    RepositoryUrl: https://github.com/inedo/inedox-subversion.git,
    SourcePath: trunk,
    To: ~\Sources
);
```

This operation effectively runs the equivalent SVN command: 

```
svn.eve checkout https://github.com/inedo/inedox-subversion.git
```

*Note: the `.git` extension works in this case because GitHub also supports Subversion clients.*

### Checking-out Branches in Subversion

In Subversion, branches are simply separate paths in the source tree. Many organizations use branches to determine the specific code that will be built for a given release. 

In BuildMaster, checkout out a branch is as easy as updating the `SourcePath` in the get latest example above:

```
Svn-Checkout(
    RepositoryUrl: https://github.com/inedo/inedox-subversion.git,
    SourcePath: branches/v1.2.5,
    To: ~\Sources
);
```

A more general approach can be taken by using BuildMaster functions (e.g. `SourcePath: branches/$ReleaseNumber`), configuration variables, or even [release template variable prompts](/docs/buildmaster/releases/templates#components) that enable the branch to be selected at build time.

### Branching and Tagging in Subversion

As noted earlier, branches are simply separate paths in source code. Branches could be created automatically at any stage during a pipeline, for example, to create a "release" branch for the next release in your application cycle.

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

Similarly, tagging in Subversion follows the same process of copying source paths. Tags can be thought of as "readonly snapshots" of code in a repository at a particular moment in time where the tag is a descriptive identifier (e.g. `Release-1.2.5`).

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

{.attention .analogy} Because the branching/tagging operations require write access, they will likely require [authentication](#authentication) with the repository.

## Continuous Integration {#ci data-title="Continuous Integration"}

### Automated Builds

BuildMaster supports automatically monitoring a Subversion repository for changes, no matter where it is hosted. 

To automatically create builds when developers commit to a Subversion repository, simply configure a [Repository Monitor](/docs/buildmaster/builds/continuous-integration/repository-monitors).

{.attention .technical} Note: a Subversion repository monitor requires BuildMaster v6.1 or later in combination with v1.1.0 or later of the Subversion extension.

#### Available Variables

When using a [repository monitor plan](/docs/buildmaster/builds/continuous-integration/repository-monitors#ci-plans), the following variables are available:

{.docs}
 - `$Branch` - the full path of the branch, e.g. `branches/develop-1.2.3`
 - `$RevisionNumber` - the highest integer revision number of any file within the specified path

#### Built-in Subversion Client vs. `svn` CLI {#cli data-title="Built-in SVN Client vs. SVN CLI"}

By default, BuildMaster does not require SVN to be installed on a build server in order to perform source control operations. However, some users who require more advanced configuration for their SVN integration may instruct BuildMaster to run the CLI instead by:

{.docs}
 - setting the `SvnExePath` operation property to a valid SVN executable path, e.g. `/usr/bin/svn` on Linux or `C:\Program Files\Subversion\bin\svn.exe` on Windows
 - configuring a `$SvnExePath` variable at the server or system level in BuildMaster; this will force all Subversion source control operations to use the CLI instead of the built-in library