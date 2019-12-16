---
title: Continuous Integration
subtitle: GitLab Continuous Integration
sequence: 200
keywords: buildmaster, gitlab, git, continuous-integration
show-headings-in-nav: true
---

In addition to simple source control integration, GitLab can also be used for continuous integration (or CI). In this page, you'll find detailed instructions on making the most of BuildMaster's integration point with GitLab for CI.

## Configuring GitLab Webhook Monitors {#webhooks data-title="Configuring Webhooks"}

See the [GitLab Webhooks](webhooks) documentation for step-by-step instructions on configuring webhooks.

## Automatic Build Triggers {#automatic-build-triggers data-title="Automatic Build Triggers"}

Automatic Build Triggers are primarily designed to monitor your [GitLab source control repository](source-control) and create a new [build](/docs/buildmaster/builds/overview) whenever someone makes changes. This is generally called continuous integration (CI) and provides immediate feedback on problems with code changes, whether through basic compilation or by running [unit tests](/docs/buildmaster/builds/tests/unit-tests) and other automated tests. This also means builds are ready to deploy sooner for further testing.

This feature isn't limited to only creating builds on change, and you could use a Scheduled Build Trigger to deploy a build to a testing environment on a nightly basis.

BuildMaster supports three general types of build triggers:   

{.docs}
-	[Source Control Repository Monitors](/docs/buildmaster/builds/continuous-integration/build-triggers-and-monitors/repository-monitors) will watch a GitLab repository for new commits
-	[Webhook Monitors](/docs/buildmaster/builds/continuous-integration/build-triggers-and-monitors/gitlab-hooks) will listen for webhook events that you configure in your GitLab account
-	Scheduled Build Trigger will execute on a daily, weekly, or custom interval

The default behavior of each of these triggers is to create a new build in either all active releases, the latest release, or a specific release (which could be a number or a variable, such as an application-defined `$TriggerReleaseNumber`).

### Executing Custom OtterScript on Trigger {#custom-plan}

You can also configure a build trigger to execute a custom plan, which allows you to enter the name of an application-level or system-level OtterScript plan.

During execution, any additional context from the commit will be available via deployment variables to support more complex use-cases:

{.docs}
-	Automatically creating a release & build when a new branch is created by combining the `Ensure-Release` and `Create-Build` operations
-	Sending an email notification when a change is detected using the `Send-Email` operation
-	Create builds for multiple releases or spanning multiple applications

#### Example

The following OtterScript is essentially what will execute when you configure a Git repository monitor that creates builds against the latest release and select the option to create `$Branch` and `$CommitHash` variables on build.

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

This is mostly for reference; if you wanted this exact behavior, you should just use the built-in "create a new build" function.

### System-level & Shared Build Triggers {#system-shared-triggers}

You can also create build triggers at the system-level, which can be particularly useful if you have a lot of similar applications that use similar conventions.

For example, on our [public instance of BuildMaster](https://buildmaster.inedo.com/), a number of our applications are configured to use a shared GitHub webhook monitor and has some advanced selection logic to determine which application to create a build in. While the example is for GitHub, the plans and process would be identical for integrating with GitLab:

```
Log-Debug "Webhook received for $Repository repository (Branch=$Branch)";
if $Repository == inedo-docs
{
    Create-Build
    (
        Application: Docs.Inedo.Com,
        Variables: %(Branch: $Branch)
    );
}
else if $Branch == master
{
    if $MatchesRegex($Repository, ^inedox-.`$*)
    {
        set $appName = $Substring($Repository, 7);
        Log-Debug "Looking for $appName application...";
    
        Log-Debug `$ApplicationExists(`$appName) = $ApplicationExists($appName);
    
        if $ApplicationExists($appName)
        {
            Log-Debug "$appName application found. Creating build...";
        
            Create-Build
            (
                Application: $appName
            );
        }
    }
    
    if $Repository == upack || $Repository == Inedo.UPack
    {
        Create-Build
        (
            Application: $Repository
        );
    }
    
    if $Repository == inedo-docs-jp
    {
        Create-Build
        (
            Application: InedoDocs-jp,
            Variables: %(Branch: $Branch)
        );
    }
}
```

## Displaying a CI Badge in a GitLab Project {#ci-badge data-title="CI Status Badges"}

GitLab supports CI badges per project. Before they can be used, the [CI Badge API](/docs/buildmaster/reference/api/ci-badge) must be enabled. In GitLab, visit the *Settings* > *General* tab, and find the Badges section. Adding the following link and image URLs will render a badge on the project overview page:

{.docs}
 - **Link** - `https://{buildmaster-server}/api/ci-badges/link?key=<badge-key>&$ApplicationName=%{project_path}`
- **Badge image URL** - `https://{buildmaster-server}/api/ci-badges/image?key=<badge-key>&$ApplicationName=%{project_path}`

Refer to the [CI Badges](/docs/buildmaster/builds/continuous-integration/badges) documentation for examples with further customization.