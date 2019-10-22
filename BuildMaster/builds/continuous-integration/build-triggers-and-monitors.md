---
title: Build Triggers & Scheduled Jobs
sub-title: Automatic Build Triggers & Scheduled Jobs
show-headings-in-nav: true
sequence: 200
---

Automatic Build Triggers are primarily designed to monitor your [source control repository](/docs/buildmaster/builds/continuous-integration/source-control) and create a new [build](/docs/buildmaster/builds/overview) whenever someone makes changes. This is generally called *continuous integration* and provides immediate feedback on problems with code changes, whether through basic compilation or by running [unit tests](/docs/buildmaster/builds/tests/unit-tests) and other automated tests. This also means builds are ready to deploy sooner for further testing.

This feature isn't limited to only creating builds on change, and you could use the a Scheduled Build Trigger to deploy a build to a testing environment on a nightly basis.

BuildMaster supports three general types of build triggers:

{.docs}
 * **[Source Control Repository Monitors](/docs/buildmaster/builds/continuous-integration/build-triggers-and-monitors/repository-monitors)**  will watch a Git or Subversion repository for new commits
 * **[Webhook Monitors](/docs/buildmaster/builds/continuous-integration/build-triggers-and-monitors/repository-hooks)** will listen for webhook events that you configure in your GitLab or GitHub account; see [GitLab Webhook Monitors](https://github.com/Inedo/inedox-git/wiki/configuring-gitlab-hooks) and [GitHub Webhook Monitors](https://github.com/Inedo/inedox-git/wiki/configuring-github-hooks)
 * **Scheduled Build Trigger** will execute on a daily, weekly, or custom interval

The default behavior of each of these triggers is to create a new build in either all active releases, the latest release, or a specific release (which could be a number or a variable, such as a application-defined `$TriggerReleaseNumber`). 

##  Executing Custom OtterScript on Trigger {#custom-plan data-title="Executing Custom Plan"}
You can also configure a build trigger to *execute a custom plan*, which allows you to enter the name of an application-level or system-level OtterScript plan.

During execution, any additional context from the commit will be  [available via deployment variables](/docs/buildmaster/builds/continuous-integration/build-triggers-and-monitors/repository-monitors#ci-plans)  to support more complex use-cases:

{.docs}
-   Automatically creating a release & build when a new branch is created by combining the  `Ensure-Release`  and  `Create-Build`  operations
-   Sending an email notification when a change is detected using the  `Send-Email`  operation
-   Create builds for multiple releases or spanning multiple applications

### Example OtterScript 
The following OtterScript is essentially what will execute when you configure a Git repository monitor that creates builds against the latest release, and select the option to create `$Branch` and `$CommitHash` variables on build.
 
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

##  System-level & Shared Build Triggers {#system-level data-title="System-level & Shared Build Triggers"}

You can also create build triggers at the system-level, which can be particularly useful if you have a lot of similar applications that use similar conventions.

For example, on our [public instance of BuildMaster](https://buildmaster.inedo.com/), a number of our applications are configured to use a shared GitLab webhook monitor, and has some advanced selection logic to determine which application to create a build in: 

````
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
````
