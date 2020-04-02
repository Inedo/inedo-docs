---
title: Backing-up & Restoring Applications
sequence: 300
show-headings-in-nav: true
---

First and foremost, you should always [routinely backup your entire BuildMaster instance](/docs/buildmaster/installation-and-maintenance/backing-up). You never know when hardware could fail, or other problems could happen. 

But there are also times where you may want to backup and restore individual applications: perhaps before a major refactoring, or as part of a migration from one instance of BuildMaster to another.

{.attention .best-practice} Application Backup & Restore are only available in BuildMaster 6.2 (or BuildMaster 6.1.12+ with preview features enabled)

## Backing Up an Application

To backup an application, first create a [secure resource](/docs/buildmaster/ci-cd/continuous-integration/packaging/secure-resources) that points to a ProGet Universal Feed feed that BuildMaster has access to publish packages to, or a Universal Package Feed disk path. 

In order to use a Universal Package Feed disk path, you will need to create a Universal Package Feed [secure resource](/docs/buildmaster/ci-cd/continuous-integration/packaging/secure-resources). Instead of specifying a ProGet feed in the _API URL_, you will need to specify a disk path in the format of `file://{PACKAGE PATH}` (i.e., `file://C:/BmApplicationBackups`).

### What's Included {#included data-title="What's Included"}

Backing-up an application will package all of the application's configuration (e.g. name, variables, configuration files, deployment plan, etc.) *and* all of the application's history (builds, releases, executions, etc.) into a universal package, and then publish that package to the selected package soruce. For large applications with a lot of history, this will take a while. 

## Backing up All Applications {#backing-up data-title="Backing up All Applications"}
You can back-up multiple applications by going to Admin > Backup Applications. This performs the same backup procedure described above against all of the selected applications.

## Restoring an Application from Backup{#restoring-an-application data-title="Restoring an Application"}

You can restore one or more applications by going to Admin > Restore Applications, selecting a package source, the  application backup packages to restore from that source, and the restoration options.

 * Restore history; when selected, historic release, build, execution data will be restored with the applications
 * Overwrite behavior; this determines what will happen if an application with the same name exists
   - Default (do not overwrite); an error will be raised and the restore will fail
   - Rename Restored Application; the imported application will be given a new, system-generated name (like "ProfitCalc-Restore1")
   - Merge Configuration; configuration (variables, plans, etc) will be created or overwritten from the backup

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
