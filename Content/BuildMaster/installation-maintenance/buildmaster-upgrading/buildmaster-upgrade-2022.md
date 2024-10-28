---
title: "Upgrading to BuildMaster 2022"
nav-title: "BuildMaster 2022"
order: 3
url-slug: "buildmaster-upgrade-2022"
---

:::(Error) (Skip BuildMaster 2022: Upgrade Directly to BuildMaster 2024)
If you are using BuildMaster 6.1, BuildMaster 6.2, or BuildMaster 7.0, we recommend directly [Upgrading to BuildMaster 2024](/docs/buildmaster-upgrade-2024). If you using an earlier version, you will first need to [Upgrade to BuildMaster 6.1](/docs/buildmaster-upgrading-to-6-1).
:::

BuildMaster 2022 is a major update, and this article contains information about what will change, how it will affect your instance, and how you can minimize risk during the upgrade.

## Planning for Your Upgrade
BuildMaster 2022 is a major upgrade, and many of the changes were additional features, platform updates (library upgrades, etc.), and UI changes. Several existing features are considered deprecated.

There are some important things to consider before upgrading:
1. **Reworked integration of Git servers (GitHub, GitLab, etc.) and CI servers (Jenkins, TeamCity)**, which may affect the use of Operations, Repositories, and Monitors
 2. **Pipeline Event Listeners and Variable Prompts** have been added to replace Pre/Post Steps and Release Templates 
3. **PowerShell Operation Changes** can cause scripts in BuildMaster to issue warnings 
4. **Hybrid User Directories have been replaced**, and you will need to manually delete old entries 
5. **The platform (.NET 4.5.2) has been updated to .NET 6**, which may mean you need to update the hosting configuration and directories. Extensions you have created yourself must be recompiled

You can also revert to BuildMaster 7.0 if there are problems without restoring your database.

### Upgrading from BuildMaster 7.0
:::(Info)
Upgrading fromBuildMaster 7.0 should be relatively easy
:::

No major features have been removed, so the upgrade should be relatively easy and no preparation is required. However, given the changes to the pipelines and execution engine, we recommend testing builds and deployments after the upgrade.

You can safely downgrade to BuildMaster 7.0 without rolling back the database, but new data, such as Git connections, that you created in BuildMaster 2022 may not work in the older version.

### Upgrading from BuildMaster 6.2 
:::(Info)
Upgrading from BuildMaster 6.2 should be relatively easy.
:::

If you're using BuildMaster 6.2, we generally recommend a direct upgrade to BuildMaster 2022 instead of incrementally upgrading to BuildMaster 7.0.

No major features were removed or changed in BuildMaster 7.0, so an incremental upgrade makes little sense. See the [Upgrading to BuildMaster 7.0](/docs/buildmaster-upgrading-to-v7) guide for all the changes.

Most of the changes will be in BuildMaster 2022. So read through exactly what has changed to see how it might affect you. If you have problems after upgrading to BuildMaster 2022, you can downgrade to BuildMaster 7.0 without restoring the database, or simply roll back to BuildMaster 6.2 after restoring the database.

### Upgrading from BuildMaster 6.1 or Earlier
:::(Warning)
If you're upgrading from BuildMaster 6.1 or earlier, upgrading to BuildMaster 2022 may be challenging.
:::
In BuildMaster 6.2, all [Legacy Features](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features) were removed from the product. See [Upgrading to BuildMaster 6.2](/docs/buildmaster-upgrading-to-6-2) to learn more about what else changed in that release. Alternatively, you may want to consider an [application-by-application migration approach](/docs/buildmaster/installation-maintenance/buildmaster-migrating-instance-to-new-server#migrating-applicationbyapplication).

You wont be able to upgrade to BuildMaster 2022 until the [Legacy Feature Detector](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features#legacy-feature-detector) has reported no legacy features. This is only available in the latest versions of BuildMaster 6.1, which means you will need to [upgrade to BuildMaster 6.1](/docs/buildmaster-upgrading-to-6-1) first.

Because upgrading from BuildMaster 6.1 is more involved than previous upgrades, special support for it has been included with paid licenses. Simply use the [Submit Ticket Form](https://my.inedo.com/tickets/new), put `2022 UPGRADE` in the "How can we help" section, and include the [Legacy Features Dashboard Report Logs](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features#legacy-feature-detector) in the text body of the ticket.

## New Features in BuildMaster 2022

### New Feature: First-Class Git Server (GitHub, GitLab, etc.) Integration
BuildMaster 2022 is now tightly integrated with Git services such as GitHub, GitLab, Azure DevOps, and BitBucket. This allows you to:
* Browse branches/commits directly from BuildMaster to quickly create new builds
* Compare commits (code changes) between two builds/releases
* Tag commits on release
* Create and merge pull requests
* Monitor repositories to create or deploy builds

See our [Git integration documentation](/docs/buildmaster/builds-continuous-integration/buildmaster-git-source-control) to learn more.

This feature required significant changes to the various Git extensions (Git, GitHub, GitLab, and AzureDevOps). Instead of using service-specific operations like ` GitHub::Get-Source`, you can simply use operations like `Git::Checkout-Code`. The service-specific operations will still work, but will issue a warning.

Git repositories (which are still managed via Secure Resources) can now be synced with BuildMaster. Your existing repositories will show as remote only and you will be prompted to sync them. This is not required.

Builds can now be associated with a Git commit of a synchronized repository; this eliminates the need to record variables such as $Branch or $Commit in your build. However, if you need the branch or commit associated with a build, you can use $Branch or $Commit - they are now variable functions that return the associated information.

Repository Monitors were also been rewritten/replaced with Resource Monitors. They behave essentially similarly, but it is no longer possible to specify credentials (user name/password) directly in the monitor, run the monitoring on a different server, or use git.exe. The old Repository Monitor configuration will remain in the database for rollback purposes.

:::(Warning) (Git Repositories Risk Mitigation)
After the upgrade, make sure your existing Git operations and monitors are working as expected. If major problems occur, plan to roll back to BuildMaster 7.0. Over time, plan to transition to synchronized repositories, new operations, and commit-associated builds.
:::

### New Feature: First-Class CI Server (Jenkins, TeamCity) Integration
See our [CI Server integration documentation](/docs/buildmaster/deployment-continuous-delivery/buildmaster-ciserver-import) to learn how BuildMaster 2022 lets you:
* Browse builds on Jenkins and TeamCity to see parameters and artifacts
* Import artifacts from builds you select
* Create monitors that will automatically import when new builds

This feature also involved substantial changes to Jenkins and TeamCity extensions, but the operations are still the same (e.g. it’s still `Jenkins::Import-Artifacts`).

CI Projects (still managed by Secure Resources) are now regularly synced with BuildMaster, which means you will be able to see build information directly in BuildMaster. This can cause problems if you are using older versions of Jenkins or TeamCity.

Builds can now be associated with a CI Build; this eliminates the need to capture variables such as $JenkinsBuildNumber, and if you need this information, you can use the $CIBuild variable function.

:::(Warning) (CI Server Integration Risk Mitigation)
After the upgrade, ensure that your existing CI operations are working as expected. If there are major problems, plan to roll back to BuildMaster 7.0.
:::

### New Feature: Application Onboarding & Navigation Changes
Both top-level and application-level navigation has changed significantly. Of particular note:
* Global Pipelines and Global Scripts are accessible in each application (under the Global tab) or via a link in Administration
* The single Application Settings page combines many other pages, such as variables, resources, credentials, and monitors

When creating a new application, users are now guided through configuring a Git repository or CI Server connection. They can still create Blank Applications or Clone existing ones, or use Templates and Samples.

:::(Warning) (Application Onboarding & Navigation Changes Risk Mitigation)
There is really no risk reduction here. As with all UI changes, it will take a little time to get used to... but we believe this is better for both experienced and new users.

If you find it to be really off-putting, please [contact our product engineers](https://inedo.com/support) and give us feedback so we can improve.
:::


### New Feature: Pipeline Event Listeners
Pipelines now have [Event Listeners](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines/buildmaster-pipeline-event-listeners) that can perform actions such as sending emails, setting statuses, or tagging Git repositories when certain things happen to a build:
* Pipeline Complete; when a build has reached the last stage of the pipeline. This is often used to set statuses and create new versions.
* Build Rejected; when a build has been rejected and will not exit the pipeline. This is commonly used for tearing down temporary environments/resources.
* Failed Deployment; when there are errors deploying to a stage. This is commonly used for notifications.
* Successful Deployment; when a successful deployment has occurred at a stage. This is often used for notifications.
* Before Deployment; immediately before deployment at a stage. This is commonly used for setting variables.

Any Pre- and Post-deployment steps that you've created will be converted to Event Listeners.
* Pre-deployment Step → Before Deployment Event Listener 
* Post-deployment step (Failed) → Failed Deployment Event Listener
* Post-deployment step (Success) → Successful Deployment Event Listener

Post-deployment options will also be converted to Event Listeners:
* Cancel earlier (lower-sequenced) releases → Pipeline Complete Event Listener
* Create a new release → Pipeline Complete Event Listener
* Mark as deployed → Pipeline Complete Event Listener

The conversion happens when the pipeline is loaded, either at UI or at execution time. This means that you'll always get an Unsaved Changes notice when you want to edit a pipeline with Pre or Post steps or options.

:::(Warning) (Pipeline Events  Risk Reduction)
Ensure that pipelines that use Pre- and Post-deployment steps continue to work as before. Note that if you save changes to the pipeline, it will automatically switch to using events—which will not be compatible with BuildMaster 7.0.

If you need to rollback, you will also need to revert all edited pipelines, which you can do by deleting the top-level version at Admin > Raft Repositories.
:::

### New Feature: Pipeline Variable Prompts
Users have always been able to [prompt for variable values](/docs/buildmaster/otterscript-execution-engine/buildmaster-variables/buildmaster-variable-prompts) at build, release, and deployment time. 
* In BuildMaster 5.3, we introduced Release Templates to allow users to define different prompts based on different release types.
* In BuildMaster 7.0, we added more variable prompts types.
* In BuildMaster 2022, we added variable prompts to Pipelines, which let you prompt users for values at build, release, and deployment time. 

:::(Error) (Release Templates Deprecated)
This feature makes Release Templates redundant, so we've deprecated them.
:::

We have a utility that can perform the migration for you. You can access it by navigating to Release Templates in any application. This migration is relatively low risk, as it simply moves the variable prompts you define from the release template into the pipeline.

:::(Warning) (Migration Risk Reduction)
There is no need to rush when migrating your variable prompts from Release Templates to Pipelines. When you are ready to do this
* do a "dry run" of the migration and check the logs to see which warnings and which variable prompts are migrated
* export your raft (Admin > Raft Repositories ) before the migration so you can re-import your exported raft later on
* test a handful of create build and deploy build pages that contain key prompts that you want to make sure work as expected
:::


### New Feature: Expanded Scripting Language Support

BuildMaster 2022 has first-class support for multiple scripting languages: PowerShell, Python, Windows Batch, and OtterScript.

This will allow you to upload your existing scripts and run them directly in your build and deployments.

:::(Warning) (Scripting Risk Mitigation)
Be prepared for OtterScripts that use PowerShell operations (i.e. `PSCall` or `PSCall`) to have errors, and to fix those by editing the OtterScript.
See the [PowerShell Operation Changes](#powershell-operation-changes) to learn more.
:::

### New Feature: BuildMaster Basic Edition

This is a new edition of BuildMaster supported via a new license key in BuildMaster 2022. BuildMaster Basic and BuildMaster Enterprise have almost all the same features. However, BuildMaster Enterprise adds the ability to run in a high availability cluster, use SAML Sign-on, and support more users.

### New Feature: SAML & Security Management Overhaul

We have increased the flexibility of logging into BuildMaster and made managing security much easier:

* Improved UI for managing security and users
* Allow multiple user directories to be searched at once
* Integration with SAML providers such as Azure and PingID
* Import/export Permissions and Built-in Users
* Allows multiple login options at once: Built-in user name/passwords, Windows Authentication, SAML Authentication

These changes have changed some of the login/authentication codes, which may cause errors/regressions where users cannot log in or a connected system (build/CI server) reports authentication errors.

Several task attributes were also renamed/combined:
* `ChangeControls_Manage` was removed, since the feature was removed in BuildMaster 6.2
* `Notes_Manage` became `Builds_EditNotes`, and `Builds_AddNotes` was added
 * `Issues_Manage` was renamed to `Release_ManageIssues`
* `ScriptAssets_Manage` and ` Plans_Manage` was merged into `Scripts_Manage`
* `ScriptAssets_ViewContents` and ` Plans_ViewContents` were merged into `Scripts_ViewContents`
* `Applications_ViewSources` was added

Also, if you are using the **Hybrid User Directory** (which allows you to query multiple user directories), these user directories will be enabled and you will get an error message on the User Directories page if you had one configured. You can safely delete this directory.

:::(Warning) (Security Changes Risk Mitigation)
Be prepared to run the [locked out troubleshooting](/docs/installation/security-ldap-active-directory) in the event you are locked out after upgrade. You will be able to restore security after that. If that doesn't work, you can roll back to BuildMaster 7.0.
:::

### New Feature: Script Templates
When you create a new script, in many cases you'll be prompted to use a Script Template such as Build . NET Application or Deploy IIS Site to use. Under the hood, these are regular OtterScripts that use a simplified editor.

### New Feature: New OtterScript Editor

We have completely rewritten the OtterScript editor. In addition to greatly improved usability, the most notable changes are the following:

* New "statement searcher" for adding statements and operations
* Automatic saving of draft changes with the ability to return to them later and edit them
* Commit/review changes before finalizing/saving them
* Autocomplete (i.e. like IntelliSense) when editing OtterScript in Text Mode

:::(Warning) (New OtterScript Editor Mitigation)
There is virtually no risk with the new code editor, but it may take a little getting used to:
 * The Add Statement Sidebar was removed, and users will need to click +[Statement] buttons under each block
 * Changes need to be Committed to take effect, as they are automatically saved while you edit
:::

## PowerShell Operation Changes
To standardize usage/syntaxes across different scripting languages, PowerShell Operations (`PSEnsure`, `PSVerify`, and `PSCall`) were rewritten and have a slightly different syntax:

* A file extension (`.ps1) needs to be used 
* Script parameters need to be specified as a map within the `Parameters` argument

Generally, BuildMaster users will only use the `PSCall` operation, which executes a PowerShell script stored in BuildMaster. However, if you're sharing scripts between Otter, it's possible that you'll use `PSEnsure`, which would effectively be the same thing. BuildMaster will not execute a `PSVerify` operation.

To minimize the impact on your existing OtterScript-based scripts, BuildMaster can detect if you're using the old syntax by looking for a file extension. 
* At Runtime, BuildMaster will use the old operations 
* In the Visual Editor, BuildMaster will automatically convert to the new style

:::(Info) How to Migrate
The easiest change is to explicitly use the old operation (i.e., `PSEnsure1`, `PSVerify1`, and `PSCall1`). Just change `PSCall` to `PSCall1`.

Alternatively, you can edit the parameters to follow the new format.
:::


### Syntax Differences
```
# Old Syntax
PSCall CreateLocalAdmin
(
    User: $PSCredential(LocalAdminAlex)
);

# New Syntax
PSCall CreateLocalAdmin.ps1
(
    Parameters: %(User: $PSCredential(LocalAdminRich))
);
```

### Implementation Details

Behind the scenes, these changes required us to create an entirely new set of operations.
 * Old operations: `PSEnsure1`, `PSVerify1`, `PSCall1`
 * New operations: `PSEnsure2`, `PSVerify2`, `PSCall2`

In BuildMaster v2022, `PSEnsure`, `PSVerify`, and `PSCall` are aliases for `2`, and the aliases are preferred. This means you should not explicitly use `PSEnsure2`, `PSVerify2`, `PSCall2` in your OtterScript.


## Custom Extensions Changes
BuildMaster 2022 targets `Inedo.SDK-2.2`, which means that you'll need to update the `Inedo.SDK` NuGet package reference in your project to Inedo-SDK 2.2. 

Note that this version of the SDK targets .NET6.

See [creating an extension documentation](/docs/inedosdk/extending-inedo-tools-using-the-sdk/inedosdk-extending-creating) to learn more about how to do this.


## Upgrade Process
You can only upgrade from BuildMaster 6.1 and later.

You can perform the upgrade from the Inedo Hub. As with all upgrades, you should make sure your [installation is backed-up](/docs/installation/backing-up-restoring).

After the installation you have to update/install new versions of the extensions if needed.

If you are upgrading from BuildMaster 6.1, you will need to perform some additional steps after upgrading to BuildMaster 2022:
1. Remove old ( Legacy SDK) extensions as required 
2. If you are using the `SqlServer 1.0.0` extension, it will not load; delete it and download `SqlServer 2.0.0`
3.  Upgrade/install  extensions as needed

## Rolling Back

You can roll back using the Inedo Hub.

:::(Info)
**You can roll back to any version of BuildMaster 7.0 without restoring the database.**
:::

Although there are changes to the database schema, they are all backward compatible, which means that you can safely reset your BuildMaster installation if an error occurs, and then upgrade later.
