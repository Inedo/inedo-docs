---
title: Backing-up & Restoring Applications
sequence: 60
show-headings-in-nav: true
---

First and foremost, you should always [routinely backup your entire BuildMaster instance](/docs/buildmaster/installation-and-maintenance/backing-up). You never know when hardware could fail, or other problems could happen. But there are also times where you may want to backup and restore individual applications: perhaps before a major refactoring, or as part of a migration from one instance of BuildMaster to another.

{.attention .best-practice} Application Backup & Restore are only available in BuildMaster 6.2 (or BuildMaster 6.1.12+ with preview features enabled)

To backup an application, first create a [package source](/docs/buildmaster/builds/packaging/package-sources) that points to a ProGet feed that BuildMaster can publish packages to. You shouldn't use the `Templates' package source for this purpose.

Then, just go to the Advanced Settings page in the application and click the "Backup Application" button. This will export your entire application (including history), and publish it to the target ProGet feed. For large applications with a lot of history, this will take a while. 

## Backing up All Applications {#restoring-an-application data-title="Backing up All Applications"}
{.upcoming} This can currently done by going to Admin > Export applications, but a specific feature will be added.


## Restoring an Application from Backup{#restoring-an-application data-title="Restoring an Application"}
{.upcoming} This can currently done by going to Admin > Import applications, but a specific feature will be added.

## Routinely Backup Your Applications{#routine-backup data-title="Routinely Backup Your Applications"}
Adding backups as part of your regularly scheduled maintenance will ensure that all of your applications will be easily restorable should something happen to break your workflow. In order to set this up you will need to create a [scheduled job](/docs/buildmaster/builds/continuous-integration/build-triggers-and-monitors) that runs a custom plan with simple OtterScript that called `System::Backup-Application`

Here is an example: 
```
System::Backup-Application
(
    Application: HDars,
    PackageSource: BuildMasterApplications
);
```
