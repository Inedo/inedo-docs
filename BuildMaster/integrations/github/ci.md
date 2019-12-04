---
title: Continuous Integration
subtitle: GitHub Continuous Integration
sequence: 100
keywords: buildmaster, github, git, continuous-integration
show-headings-in-nav: true
---

In addition to its use as a hosting platform primarily for open-source projects, it can also be used for continuous integration. In this page, you'll find detailed instructions on making the most of BuildMaster's integration point with GitHub for CI.

## Configuring GitHub Webhook Monitors {#webhooks data-title="Configuring Webhooks"}

See the [GitHub Webhooks](webhooks) documentation for step-by-step instructions on configuring webhooks.

## Automatic Build Triggers {#automatic-build-triggers data-title="Automatic Build Triggers"}

Automatic Build Triggers are primarily designed to monitor your [GitHub source control repository](source-control) and create a new [build](/docs/buildmaster/builds/overview) whenever someone makes changes. This is generally called continuous integration and provides immediate feedback on problems with code changes, whether through basic compilation or by running [unit tests](/docs/buildmaster/builds/tests/unit-tests) and other automated tests. This also means builds are ready to deploy sooner for further testing.

This feature isn't limited to only creating builds on change, and you could use a Scheduled Build Trigger to deploy a build to a testing environment on a nightly basis.

BuildMaster supports three general types of build triggers:

{.docs}
-	[Source Control Repository Monitors](/docs/buildmaster/builds/continuous-integration/build-triggers-and-monitors/repository-monitors) will watch a Git or Subversion repository for new commits
-	[Webhook Monitors](/docs/buildmaster/builds/continuous-integration/build-triggers-and-monitors/github-hooks) will listen for webhook events that you configure in your GitHub account.
-	Scheduled Build Trigger will execute on a daily, weekly, or custom interval

The default behavior of each of these triggers is to create a new build in either all active releases, the latest release, or a specific release (which could be a number or a variable, such as an application-defined $TriggerReleaseNumber).

### Executing Custom OtterScript on Trigger {#custom-plan}

You can also configure a build trigger to execute a custom plan, which allows you to enter the name of an application-level or system-level OtterScript plan.

During execution, any additional context from the commit will be available via deployment variables to support more complex use-cases:

{.docs}
-	Automatically creating a release & build when a new branch is created by combining the Ensure-Release and Create-Build operations
-	Sending an email notification when a change is detected using the Send-Email operation
-	Create builds for multiple releases or spanning multiple applications

#### Example

The following OtterScript is essentially what will execute when you configure a Git repository monitor that creates builds against the latest release and select the option to create $Branch and $CommitHash variables on build.

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

For example, on our [public instance of BuildMaster](https://buildmaster.inedo.com/), a number of our applications are configured to use a shared GitHub webhook monitor and has some advanced selection logic to determine which application to create a build in:

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

## Displaying CI Status on GitHub {#displaying-ci-status data-title="GitHub CI Status"}

BuildMaster can set GitHub CI status using the GitHub::Set-Status operation which operates on a specific commit. The main use-case is to display an icon on GitHub to display the build status for BuildMaster build of the commit, and statuses can be set to:

{.docs}
-	`auto` - BuildMaster automatically chooses the status
-	`pending` - a build is about to start or is running
-	`success` - build completed successfully
-	`failure` - execution didn't complete or there was an error unrelated to build compilation/tests
-	`error` - execution completed but with a build error

An example plan that uses this operation is:

```
try
{
	GitHub::Set-Status 
	(
		CommitHash: $CommitId,
        Status: pending
    );
	
	# other build operations...
}
finally
{
	GitHub::Set-Status 
	(
		CommitHash: $CommitId,
       Status: auto
    );
}
```

## Displaying a CI Badge in a GitHub README {#ci-badge data-title="CI Status Badges"}

Refer to the [CI Badges documentation](/docs/buildmaster/builds/continuous-integration/badges) for examples on how to render a CI badge on a GitHub readme file.