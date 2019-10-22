---
title: Repository Monitors
show-headings-in-nav: true
sequence: 900
keywords: buildmaster, releases, builds, monitors
---

Repository monitors are an extensible component that can be configured to poll a source control repository at a custom interval, capturing and comparing the latest commit to determine whether a new build should be created, or a custom OtterScript plan should run.

::: {.attention .analogy}
<img src="/resources/images/icons/analogy.png" />

BuildMaster supports a repository monitor for Git repositories. Install the Git extension (v1.1 or later) to use it. Visit the [Git extension Wiki](https://github.com/Inedo/inedox-git/wiki) for more information.
:::

#### Configuring a Repository Monitor

A repository monitor may be created from the Administration > Repository Hooks & Monitors page, or from the Builds submenu in the context of an application. The following fields apply to all monitors:

{.docs}
*   **Name** - a friendly name to identify the monitor in the UI
*   **Run for application/group** - the application filter of the monitor
*   **Plan** - the deployment plan to run when a new commit is detected. When no plan is assigned to the monitor, a new build is created for the latest release. See [Custom Plans](#custom-plans) below for more information.
*   **Active** - determines whether the monitor is enabled
*   **Schedule** **-** This cron expression determines the frequency that the repository will be checked for changes. See the [Quartz Documentation](https://www.quartz-scheduler.net/documentation/quartz-3.x/tutorial/crontrigger.html) for information about this format as well as examples. The default configuration of '`0 0/4 * * * ?`' performs a check every 4 minutes.
*   **Run on server** - When specified, the repository monitor will use this server to connect to the remote repository, otherwise the polling is performed from the BuildMaster server.

The following fields apply specifically to the Git monitor:

{.docs}
*   **Branch** - specifies the branch to be monitored, using [regular expression syntax](https://docs.microsoft.com/en-us/dotnet/standard/base-types/regular-expression-language-quick-reference) to match the branch name.
*   **Credentials** - the Resource Credentials used to connect to the Git repository
*   **Repository URL** - the URL of the Git repository. By default this uses the repository defined in the credentials, or anonymous if not supplied in the credentials either
*   **Git executable path** - By default, BuildMaster uses the LibGitSharp library for Git connections, but a path to Git client may be used instead for custom configurations (e.g. SSH connections, older versions of Git)

Text fields for a monitor support variable expansion, allowing for simpler configuration. For example, a single Git repository monitor with a Repository URL of `https://github.com/inedo/inedo-$ToLower($ApplicationName)` for the "Extensions" application group would allow all descendant applications named after the extensions to automatically have builds created for them on commit.

#### Custom Plans

When a custom plan is assigned to a repository monitor, then instead of automatic creation of a new build, that plan will be executed in the context of all applications matching the specified application filter. Because of this, plans may be application-level or system-level plans, and application-level plans take precendence if both exist. During execution, any additional context from the commit will be [available via deployment variables](#ci-plans) to support more complex use-cases:

{.docs}
*   Automatically creating a release & build when a new branch is created by combining the `Ensure-Release` and `Create-Build` operations
*   Sending an email notification when a change is detected using the `Send-Email` operation
*   Create builds for multiple releases or spanning multiple applications

### Repository Hooks

BuildMaster supports custom webhook events fired by GitHub and GitLab. Visit the [Git extension Wiki](https://github.com/Inedo/inedox-git/wiki) for more information on the configuration required for each specific system.

### Hook & Monitor Plans

Both Repository Monitors and Repository Hooks support common variables that can be used within the plan. The default variables include:

{.docs}
*   `$Branch` - the ref that a commit was pushed to without `refs/heads/` at the beginning, e.g. `master`
*   `$CommitHash` - (Git) the ID, SHA, or hash associated with the commit, e.g. `525571720b56ca53d1d3229e774c7b7980d3ff2b`
*   `$RevisionNumber` - (SVN) the highest integer revision number of any file within the specified path

<div class="attention best-practice">

**Note:** these variables are _only_ available when a custom plan is specified. If using the _default_ setting (i.e. creating a build for the latest release), these variables are not available. This is primarily to support backwards compatibility of existing pipelines that rely on a configuration variable with the same name at a different scope.

</div>

To replicate the _default_ no-plan behavior with these variables in context, you may reference a system-level plan defined simply as:


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

Additional context may be available depending on the repository host. Visit one of the following for more information:

{.docs}
*   [Git extension Wiki](https://github.com/Inedo/inedox-git/wiki)
*   [Subversion extension Wiki](https://github.com/Inedo/inedox-subversion/wiki)
*   [Mercurial extension Wiki](https://github.com/Inedo/inedox-mercurial/wiki)