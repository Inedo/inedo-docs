---
title: Repository Monitors
show-headings-in-nav: true
sequence: 100
keywords: buildmaster, releases, builds, monitors
---

Repository monitors are an extensible component that can be configured to poll a source control repository at a custom interval, capturing and comparing the latest commit to determine whether a new build should be created, or a custom OtterScript plan should run.

::: {.attention .analogy}
<img src="/resources/images/icons/analogy.png" />

BuildMaster supports a repository monitor for Git repositories. Install the Git extension (v1.1 or later) to use it. Visit the [Git extension Wiki](https://github.com/Inedo/inedox-git/wiki) for more information.
:::

## Configuring a Repository Monitor {#configuring data-title="Configuring a Monitor"}

A repository monitor may be created at the system level from the *Administration* > *Build Triggers & Scheduled Jobs* page, or from the *Builds* > *Triggers* tab in the context of an application.

By default, all branches are monitored, and a new build for all active releases is created in the specified application/group, or in the specific application where it is configured. Any of these settings can be modified for each individual repository monitor.

::: {.attention .best-practice}
Text fields for a monitor support variable expansion, allowing for simpler configuration. For example, a single Git repository monitor with a Repository URL of `https://github.com/inedo/inedo-$ToLower($ApplicationName)` for the "Extensions" application group would allow all descendant applications named after the extensions to automatically have builds created for them on commit.
:::

### Custom Plans {#plans data-title="Custom Monitor Plans"}

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

Additional variables may be available depending on the source control system. Visit one of the following for more information:

{.docs}
*   [Git extension Wiki](https://github.com/Inedo/inedox-git/wiki)
*   [Subversion extension Wiki](https://github.com/Inedo/inedox-subversion/wiki)

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
