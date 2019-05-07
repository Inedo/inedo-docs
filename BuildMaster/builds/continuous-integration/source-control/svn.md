---
title: Subversion
subtitle: Working with Subversion (SVN) in BuildMaster
sequence: 100 
keywords: buildmaster, source-control, svn
---

Regardless of what kind of repository your project's source code is hosted in, BuildMaster can build it! While the most popular source control management system has become Git, BuildMaster also supports various source control operations for Subversion, including:

| Operation | Description |
|---|---|
| Checkout  | Checks out a working copy from a repository |
| Update    | Bring changes from a repository into a working copy |
| Export    | Gets the unversioned contents of a repository to a specified directory |
| Copy      | Creates a copy of a source path to facilitate branching and tagging |
| Delete    | Deletes a file in a repository  |

## How to Integrate BuildMaster with Subversion

To start using Subversion in your build plans, simply:

 1. Install the [Subversion extension](https://inedo.com/den/inedox/subversion) from `Administration` > `Extensions` or manually
 2. *(Optional)* Create a Subversion resource credential (secret) to your repository to simplify authentication (see [Authenticating to a Subversion Repository]( #authentication)

## Using Subversion within BuildMaster

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

This operation effectively runs the equivalent SVN command behind the scenes: 

`svn.eve checkout https://github.com/inedo/inedox-subversion.git`

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

A more general approach can be taken by using BuildMaster functions (e.g. `SourcePath: branches/$ReleaseNumber`), configuration variables, or even [release template variable prompts](/support/documentation/buildmaster/releases/templates#components) that enable the branch to be selected at build time.

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

### Automatic Builds & Continuous Integration with Subversion

BuildMaster supports automatically monitoring a Subversion repository for changes, no matter where it is hosted. 

To automatically create builds when developers commit to a Subversion repository, simply configure a [Repository Monitor](/support/documentation/buildmaster/builds/continuous-integration/repository-monitors).

{.attention .technical} Note: a Subversion repository monitor requires BuildMaster v6.1 or later in combination with v1.1.0 or later of the Subversion extension.

#### Available Variables

When using a [repository monitor plan](/support/documentation/buildmaster/builds/continuous-integration/repository-monitors#ci-plans), the following variables are available:

 - `$Branch` - the full path of the branch, e.g. `branches/develop-1.2.3`
 - `$RevisionNumber` - the highest integer revision number of any file within the specified path

## Authenticating to a Subversion Repository {#authentication}

BuildMaster's Subversion integration supports username/password authentication over HTTPS. This is the simplest and recommended method to authenticate with a Subversion repository.

While each SVN operation supports supplying a repository URL, username, and password, it is recommended to create a [Resource Credential](/support/documentation/buildmaster/administration/resource-credentials) for Subversion that includes the repository name, username, and password. This is not only more secure, but more convenient as the credentials are stored in one location.