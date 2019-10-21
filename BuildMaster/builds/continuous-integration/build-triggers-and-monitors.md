---
title: Build Triggers & Scheduled Jobs
sub-title: Automatic Build Triggers & Scheduled Jobs
keywords: scheduled-jobs, monitors, webhooks, repository-monitors, buildmaster, continuous-integration
sequence: 200
---
Automatic Build Triggers are primarily designed to monitor your [source control repository](/docs/buildmaster/builds/continuous-integration/source-control) and create a new [build](/docs/buildmaster/builds/overview) whenever someone makes changes. This is generally called *continuous integration* and provides immediate feedback on problems with code changes, whether through basic compilation or by running [unit tests](/docs/buildmaster/builds/tests/unit-tests) and other automated tests. This also means builds are ready to deploy sooner for further testing.

This feature isn't limited to only creating builds on change, and you could use the a Scheduled Build Trigger to deploy a build to a testing environment on a nightly basis.

BuildMaster supports three general types of build triggers:
 * **Source Control Repository Monitors**  will watch a Git or Subversion repository for new commits
 * **[Webhook Monitors](/docs/buildmaster/builds/continuous-integration/build-triggers-and-monitors/repository-hooks)** will listen for webhook events that you configure in your GitLab or GitHub account; see [GitLab Webhook Monitors](https://github.com/Inedo/inedox-git/wiki/configuring-gitlab-hooks) and [GitHub Webhook Monitors](https://github.com/Inedo/inedox-git/wiki/configuring-github-hooks)
 * **Scheduled Build Trigger** will execute on a daily, weekly, or custom interval

The default behavior of each of these triggers is to create a new build in either all active releases, the latest release, or a specific release (which could be a number or a variable, such as a application-defined `$TriggerReleaseNumber`). 

You  can also configure a build trigger to *execute a custom plan*, which will give you full control over what actions should be taken, including creating a new build, or even creating builds and releases in other applications.

### Example OtterScript for Git Repository Monitor
The following OtterScript is essentially what will execute when you configure a Git repository monitor that creates builds against the latest release, and select the option to create $Branch and $CommitHash variables on build. 
```
Set-BuildVariable Branch
(
    Value: $Branch
);
Set-BuildVariable CommitHash
(
    Value: $CommitHash
);
Create-Build
(
    Application: $ApplicationName
);
```
This is mostly for reference; if you wanted this exact behavior, you should just use the built-in *create a new build* function.
