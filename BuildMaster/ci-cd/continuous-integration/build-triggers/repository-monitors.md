---
title: Repository Monitors
show-headings-in-nav: true
sequence: 100
keywords: buildmaster, releases, builds, monitors
---

Repository monitors are a type of build trigger that can be configured to poll a source control repository at a custom interval, capturing and comparing the latest commit to determine whether a new build should be created, or a custom OtterScript plan should run.

## Supported Repositories {#repositories data-title="Supported Repositories"}

The following repository monitors are supported:

{.docs}
 - **Git** - can monitor a Git repository for new commits hosted on any platform (GitHub, GitLab, etc.); requires the Git extension to be installed
 - **Subversion** - can monitor an SVN repository for new revisions; requires the Subversion extension to be installed
 - ***Custom*** - repository monitors are customizable and can be built using the [Inedo SDK](/sdk/inedosdk/Inedo.Extensibility.RepositoryMonitors) *(see the [Git extension source](https://github.com/Inedo/inedox-git/blob/master/Git/Git.InedoExtension/RepositoryMonitors/GitRepositoryMonitor.cs) for an example implementation)*

## Configuring a Repository Monitor {#configuring data-title="Configuring a Monitor"}

A repository monitor may be created at the system level from the *Administration* > *Build Triggers & Scheduled Jobs* page, or at the application level from the *Builds* > *Triggers* tab when viewing an application. When configured at the application level, the monitor will only create builds or run plans defined in that application.

### Specifying Branches to Monitor {#branches data-title="Branches"}

By default, repository monitors check for changes on all branches, and a new build for all active releases is created in the specified application/group, or in the specific application where it is configured. Branches to be monitored are entered as a [regular expression](https://docs.microsoft.com/en-us/dotnet/standard/base-types/regular-expression-language-quick-reference), for example:

| Value | Applies to... |
|---|---|
| `master` | `master` branch only |
| `master|default` | either `master` or `default` branches |
| `.*-\d+\.\d+\.\d+` | branches that end with `-<version-number>`, e.g. `feature-3.5.0` |
| `.*hotfix.*` | branches with `hotfix` anywhere in the name |

Once a repository monitor is configured, selecting the "Monitoring `n` branches" link will open up a complete list of monitored branches and their current states. Technically speaking, these states are the values of the branch tip commit SHA hashes in Git or HEAD revision numbers in Subversion.

### Variable Resolution {#variables data-title="Variable Resolution"}

Text fields for a monitor support variable expansion, allowing for simpler configuration. For example, a single Git repository monitor with a Repository URL of `https://github.com/inedo/inedo-$ToLower($ApplicationName)` for the "Extensions" application group would allow all descendant applications named after the extensions to automatically have builds created for them on commit.

You can use any of the [variable functions](/docs/buildmaster/reference/functions) that rely on an application or release to be in context: [$ApplicationName](/docs/buildmaster/reference/functions/applications/applicationname), [$ReleaseNumber](/docs/buildmaster/reference/functions/releases/releasenumber), [$ReleaseName](/docs.inedo.com/docs/buildmaster/reference/functions/releases/releasename), [$ReleaseNumberPart()](/docs/buildmaster/reference/functions/releases/releasenumberpart), etc.

You can also use any [configuration variables](/docs/buildmaster/administration/configuration-variables) that are accessible from a release context: release, application, application-group, or system.

## Custom Plans {#plans data-title="Custom Monitor Plans"}

When a custom plan is assigned to a repository monitor, then instead of automatic creation of a new build, that plan will be executed in the context of all applications matching the specified application filter. Because of this, plans may be application-level or system-level plans, and application-level plans take precendence if both exist. During execution, any additional context from the commit will be [available via deployment variables](#variables) to support more complex use-cases:

{.docs}
*   Automatically creating a release & build when a new branch is created by combining the `Ensure-Release` and `Create-Build` operations
*   Sending an email notification when a change is detected using the `Send-Email` operation
*   Create builds for multiple releases or spanning multiple applications

### Built-in Variables {#variables}

Repository Monitors support common variables that can be referenced in the custom plan, or any plans with a build in context generated with the *Create $Branch and $CommitHash variables on build* option checked. The default variables include:

{.docs}
*   `$Branch` - the ref that a commit was pushed to without `refs/heads/` at the beginning, e.g. `master`
*   `$CommitHash` - (Git) the ID, SHA, or hash associated with the commit, e.g. `525571720b56ca53d1d3229e774c7b7980d3ff2b`
*   `$RevisionNumber` - (SVN) the highest integer revision number of any file within the specified path

::: {.attention .technical}

**Note:** Prior to BuildMaster v6.1.12, built-in variables were _only_ available when a custom plan is specified. If using the _default_ setting (i.e. creating a build for the latest release), these variables are not available. See the [example plans](#variable-plans) below as a workaround.
:::

#### Example Variable Plan {#variable-plans}

The following example plans is effectively what occurs when a new commit is detected:

<tab-block>
<tab name="Git">

```
Create-Build
(
    Application: $ApplicationName,
    Variables: %(Branch: $Branch, CommitHash: $CommitHash)
);
```

</tab>
<tab name="SVN">

```
Create-Build
(
    Application: $ApplicationName,
    Variables: %(Branch: $Branch, RevisionNumber: $RevisionNumber)
);
```

</tab>
</tab-block>

## Troubleshooting {#troubleshooting data-title="Troubleshooting"}

#### Not seeing latest commit or revision for a branch

Behind the scenes, repository monitors check for new commits/revisions every 4 minutes. On-demand checks can be done by clicking the "Refresh" icon on the monitors page. If you aren't seeing the latest commit/revision for a specific branch, check the following:

{.docs}
 - verify that the BuildMaster service is running
 - attempt to trigger the `Repository Monitor` task runner on the *Manage Service* page and look for live error logs

#### Logs

While historical logs are not recorded for repository monitors since they run very frequently, you can visit the Diagnostic Center from the Administration page to view any errors that occurred during a recent repository monitor execution. If there are no recorded logs (or no errors generated), you may also visit the *Manage Service* page and run the `Repository Monitor` task runner directly to view any live log output.