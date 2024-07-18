---
title: "Upgrading to BuildMaster 6.2  (2020)"
order: 4
url-slug: "buildmaster-upgrading-to-6-2"
---

:::(Error) (Skip BuildMaster 6.2: Upgrade Directly to BuildMaster 2023)
If you are using BuildMaster 6.1, we recommend directly [Upgrading to BuildMaster 2023](/docs/buildmaster-upgrade-2023). If you using an earlier version, you will first need to [Upgrade to BuildMaster 6.1](/docs/buildmaster-upgrading-to-6-1).
:::

BuildMaster 6.2 is a major transformative release that removes all legacy features from the product. We no longer recommend upgrading from BuildMaster 6.1 to this version, and this article is for informational purposes only.

## Planning for Your Upgrade
Because this upgrade is more involved than previous upgrades, special support for it has been included with paid licenses. Simply use the [Submit Ticket Form](https://my.inedo.com/tickets/new), put `6.2 UPGRADE` in the "How can we help" section, and include the [Legacy Features Dashboard Report Logs](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features#legacy-feature-detector) in the body text of the ticket.

Regardless of when you started using BuildMaster, you wont be able to upgrade to BuildMaster 6.2 until the [Legacy Feature Detector](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features#legacy-feature-detector) has run and reports no remaining legacy features. This means you will need to first upgrade to the latest version of Buildmaster 6.1.

::: (info) (Using BuildMaster v3 or v4?)
We recommend upgrading to BuildMaster 4.9.10, and then BuildMaster 6.1. See [Upgrading from BuildMaster v3 and v4](/docs/buildmaster/installation-maintenance/buildmaster-upgrading/upgrading-from-buildmaster-v3-and-v4) to learn more.
:::

If moving away from legacy features is infeasible, we recommend installing a new instance of BuildMaster 6.2 side-by-side, and [migrating application-by-application](/docs/buildmaster/installation-maintenance/buildmaster-migrating-instance-to-new-server#migrating-applicationbyapplication) to the new system.  Your license will allow you use both instances, and you can migrate historical data using the [application back up/restore functionality](/docs/buildmaster/modeling-your-applications/buildmaster-applications-concepts/buildmaster-applications-concepts-backup-restore).


## Changes in BuildMaster 6.2

BuildMaster 6.2 is a major upgrade, with a number of changes and significant new features. Review this article carefully before deciding when and how to upgrade.

### Breaking Change: Legacy Features Removed

All legacy components in BuildMaster have been removed. See [BuildMaster Legacy Features](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features) for a complete list of legacy features. Most of these features are hidden by default in new installations since v5 (2016). If you're still using any of these features, you should pay special attention to the migration strategies in this document.

:::(Warning) (Legacy Execution Logs Removed)
All legacy executions logs (from legacy deployment plans) are removed during the upgrade. To retain legacy execution data, you can export applications; these are then re-imported as non-legacy executions.
:::


### New Feature: Releases are Now Optional

Builds have always been tightly coupled to releases in BuildMaster, but there are many situations where builds and deployments should be done without being part of a formal "release", such as:

* Early/testing builds that are created via Continuous Integration processes and are never intended to be released
* Simple applications (e.g., documentation or other static content) that don't require a change management process outside of source control or merge requests
* Deploying imported artifacts imported from drop paths or continuous integration servers

To better support this workflow and make it easier to get started with BuildMaster, we have added the ability to create builds that aren't associated with any release.

### New Feature: Enhanced Pipeline Deployments & UX

When a build is promoted to a pipeline stage, that promotion itself is now executed using the OtterScript runtime. This enables more advanced deployment scenarios and allows you to control more aspects of the deployment with custom OtterScript. The Pipeline Editor and UX have also been rewritten and improved.

*   Run pre-deployment scripts before deployment to configure variables that will be used later in the deployment to shut down services or otherwise prepare for deployment
*   Use variable expressions for target servers or roles
*   Run post-deployment scripts that can clean up after deployment, send notifications, etc
*   Have a single, consistent logging for the entire deployment to help diagnose problems

Post-deployment tasks are recommended instead of pipeline stage event listeners.

:::(Warning) (Custom Event Listeners May Stop Working)
Existing Pipeline Stage Event Listeners are executed very differently, and custom listeners may not run. Make sure you test them first.
:::

### New Feature: Support for Externalized Assets (Rafts)

Deployment plans, OtterScript modules, scripts (PowerShell/sh), and pipelines can now be stored in external repositories called Rafts. This allows these resources to be stored in places other than the BuildMaster database, such as a Git repository or file system directory.

Prior to version 6.2, all of these assets were stored in BuildMaster's internal database and generally referenced by a surrogate key (such as a pipeline ID) rather than by name. We never supported referencing these identifiers, but if you did, they no longer work.

:::(Warning) (Scripts, Pipelines, etc. migrated to Rafts)
All of the underlying storage tables (Pipelines, Deployment Plans, Templates, etc.) will be migrated to the `RaftItems` table and existing tables dropped.
:::

#### Updated Feature: Resource Credentials

Resource credentials have been updated to split the concept of secure resources (third-party connection information) and secure credentials (secrets). This allows connections to external systems to be configured in a consistent manner, but use different types of credentials depending on the API.

Existing credentials remain untouched and should work as in previous versions and can be converted to the split style at UI.

## Upgrade Process
You can only upgrade from BuildMaster v6.1. To upgrade:

1.  Run the Legacy Feature Detector and ensure there are no features
2.  Download and run the Inedo Hub from [my.inedo.com/downloads](https://my.inedo.com/downloads) or use the latest [traditional installer](https://my.inedo.com/buildmaster/versions#v6.1)
3.  Remove old (Legacy SDK) extensions as appropriate
4.  If you are using the `SqlServer 1.0.0` extension, it will not load; delete it and download `SqlServer 1.7.0`
5.  Upgrade/install the 6.2 extensions as needed


### Risk Mitigation

* Make sure your BuildMaster database has been backed up before upgrading
* Make sure that your Encryption Key is backed up before the upgrade
* Ensure that the installation's \\Extensions directory is backed up before upgrading

### Rollback

Since there are changes in the database, a rollback requires uninstalling BuildMaster and then restoring your BuildMaster instance.