---
title: Subversion
subtitle: Working with Subversion (SVN) in BuildMaster
sequence: 100 
keywords: buildmaster, source-control, svn
---

### Pre-requisites:
 - Subversion extension ([more info](https://inedo.com/den/inedox/subversion))

BuildMaster can build any type of project, whether greenfield, legacy applications, microservices, and more. It doesn’t matter where your project's source code is hosted.

While the most popular source control management system is Git,   BuildMaster supports various source control operations for Subversion, including:

| Operation | Description |
|---|---|
| Checkout  | Checks out a working copy from a repository |
| Update    | Bring changes from a repository into a working copy |
| Copy      | Creates a copy of a source path to facilitate branching and tagging |
| Delete    | Deletes a file in a repository  |

## Use-Cases

### Checkout a Subversion Repository

The most common use-case when working with Subversion is getting the latest source code before creating a build artifact. To accomplish this, the `Svn-Checkout` operation is used in a plan:

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

### Create a Branch

In Subversion, branches are separate paths in source code. Branches could be created automatically at any stage during a pipeline, for example, to create a "release" branch for the next release in your application cycle.

To create a branch, use the `Svn-Copy` operation:

```
Svn-Copy(
    RepositoryUrl: https://github.com/inedo/inedox-subversion.git,
    From: trunk,
    To: branches/$ReleaseNumber
);
```

This operation executes the equivalent SVN command when run under the context of release 1.2.5:

`svn.exe copy trunk branches/v1.2.5`

Because this operation requires write access, it will likely require [authentication](#authentication) with the repository.

### Automatic Builds

BuildMaster supports automatically monitoring a Subversion repository for changes, no matter where it is hosted. To automatically create builds when developers commit to a Subversion repository, simply configure a [Repository Monitor](/support/documentation/buildmaster/builds/continuous-integration/repository-monitors).

*Note: a Subversion repository monitor requires BuildMaster v6.1 or later in conjunction with v1.0.3 of the Subversion extension.*

## Authentication {#authentication}

BuildMaster's Subversion integration supports username/password authentication over HTTPS. This is the simplest and recommended method to authenticate with a Subversion repository.

While each SVN operation supports supplying a repository URL, username, and password, it is recommended to create a [Resource Credential](/support/documentation/buildmaster/administration/resource-credentials) for Subversion that includes the repository name, username, and password. This is not only more secure, but more convenient as the credentials are stored in one location.
