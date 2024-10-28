---
title: "Upgrading to BuildMaster 2024"
nav-title: "BuildMaster 2024"
order: 1
url-slug: "buildmaster-upgrade-2024"
---

BuildMaster 2024 is a major update, and this article provides information about what will change, the impact to your instance, and how to mitigate risk during upgrade.

## Planning for Your Upgrade

Although BuildMaster 2024 is consider a major update, most of the changes were relatively minor UI/UX improvements and platform/library upgrades.

### Upgrading from BuildMaster 2023
:::(Info)
Upgrading from BuildMaster 2023 should be relatively easy
:::

No major features have been removed and there were no significant changes to the core components; we anticipate the upgrade will be relatively easy and no preparation will be required. 

You can safely downgrade to BuildMaster 2023 without rolling back the database.

### Upgrading from BuildMaster 6.2/7.0/2022

If you're currently using BuildMaster 6.2, BuildMaster 7.0, or BuildMaster 2022, we recommend directly upgrading to BuildMaster 2024. While it won't *hurt* to do incremental upgrades, there's almost never a benefit.

Please read the upgrade notes from each version to learn what changed and how to mitigate risks. To summarize:

* [BuildMaster 2023](/docs/buildmaster-upgrade-2023); reworked Docker integration and Issue Tracking integration. 
*  [BuildMaster 2022](/docs/buildmaster-upgrade-2022); reworked Git integration, made pipeline changes, and PowerShell operation changes
* [BuildMaster 7.0](/docs/buildmaster-upgrading-to-v7); very little that changed from an upgrade standpoint. We mostly standardized libraries, adding Linux support, high-availability, etc.


### Upgrading from BuildMaster 6.1 or Earlier
:::(Warning)
If you're upgrading from BuildMaster 6.1 or earlier, upgrading to BuildMaster 2024 may be challenging.
:::
In BuildMaster 6.2, a significant number of [Legacy Features](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features) were removed from the product. See [Upgrading to BuildMaster 6.2](/docs/buildmaster-upgrading-to-6-2) to learn more about what else changed in that release. Alternatively, you may want to consider an [application-by-application migration approach](/docs/buildmaster/installation-maintenance/buildmaster-migrating-instance-to-new-server#migrating-applicationbyapplication).

You wont be able to upgrade to BuildMaster 2024 until the [Legacy Feature Detector](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features#legacy-feature-detector) has reported no legacy features. This is only available in the latest versions of BuildMaster 6.1, which means you will need to [upgrade to BuildMaster 6.1](/docs/buildmaster-upgrading-to-6-1) first.

Because upgrading from BuildMaster 6.1 is more involved than previous upgrades, special support for it has been included with paid licenses. Simply use the [Submit Ticket Form](https://my.inedo.com/tickets/new), put `2024 UPGRADE` in the "How can we help" section, and include the [Legacy Features Dashboard Report Logs](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features#legacy-feature-detector) in the text body of the ticket.

## All Applications: Deployments by Pipelines

The most noticeably change will likely on the "Applications" link in the top navigation. In previous versions of BuildMaster, this link would show *what versions are deployed in which environment* with a grid of applications and environments.

In BuildMaster 2024, this link now navigates to a multi-tab page that shows *current pipeline progress of builds/deployments* with a listing of applications and their pipelines.  The old view is still available on the "Deployments by Environment" tab.

## Pipeline Editor & Parallel Grouping Feature

The Pipeline Editor  page also got a bit of a UX overhaul:

* A "pipeline map" lets you filter the displayed stages to make editing long piplines easier
* Stage-specific variable inputs and prompts are displayed on the stage
* Stage-specific event listeners are now displayed under Deployment Steps

You can also specify a *parallel grouping* value on a stage target. When set (valid values are 1 to 99), this target will await for targets with a lower grouping value to complete.


## Other Notable Changes in BuildMaster 2024

Other notable changes include:
* **Insecure Git Repositories** can now be added/browsed in BuildMaster when you bypass certificate errors on the connection
* **Resource Monitors** have more options for matching newly-created builds to releases
* **Shutdown Preparation** is available under Administration and allows you to pause any new builds/deployments
* **HTTPS/SSL Settings (Integrated Web Server)** can now be edited from the Administration section

### Non-Inedo Database Objects Now Dropped

Although adding views, triggers, or stored procedures to the BuildMaster database has never been supported, some users have added them over the years.

Starting in BuildMaster 2024, all such database objects will be dropped upon upgrade. If you wish to do custom reporting with custom database views, create a
second database (e.g. BuildMasterReports) that queries your main database (e.g. BuildMaster).

### Discontinued Features in BuildMaster 2024

As discussed in our [Legacy (Deprecated) Features](/docs/buildmaster/installation-maintenance/buildmaster-legacy) guide, [Application Templates](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-application-templates) and their related features ([Variable Value Renderers](/docs/buildmaster/otterscript-execution-engine/buildmaster-variables/buildmaster-variables-customizing-display) and [Setup Templates](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-applications-concepts-setup-templates)) were removed.

With the introduction of improved application creation in BuildMaster 2022, these features became obsolete. Note that the underlying database tables ( `VariableValueRenderes` and `SetupTempaltes`) were not dropped, which means rolling back will work fine if you somehow were using these features.


### SDK Changes
BuildMaster 2024 targets `Inedo.SDK-3.0` and .NET8, and you'll need to rebuild any custom extensions you've created. See [creating an extension documentation](/docs/inedosdk/extending-inedo-tools-using-the-sdk/inedosdk-extending-creating) to learn more about how to do this.


## Upgrade Process
You should generally perform the upgrade using the same method you used to install.

* On Windows, the most common installation method is using the internet-connected [Inedo Hub](/docs/installation/windows/desktophub-overview); see  [HOWTO: Upgrade or Downgrade with the Inedo Hub](/docs/installation/windows/inedo-hub-upgrade-downgrade) for more details on how to upgrade.
* On Linux, the most common installation method is using our Docker container; see [Upgrading Docker Containers](/docs/installation/linux/installation-upgrading-docker-containers) for more details.

However, there are other installation options available, including [offline installation](/docs/installation/windows/desktophub-offline), [cluster installation](/docs/installation/high-availability-load-balancing/high-availability-load-balancing), and even [manual installation](/docs/installation/manual-installation). If you want to change installation methods, the easiest way is to simply uninstall (by following the process in reverse) and install using the new method.

If you're upgrading from BuildMaster 6.2 or earlier, it's possible BuildMaster was installed with the [legacy installer](/docs/installation/windows/installation-legacy-traditional-installer), The Inedo Hub should be able to upgrade these installations, but you may need to uninstall and then reinstall using the Inedo Hub. 

If you are upgrading from BuildMaster 6.1, you will need to perform some additional steps after upgrading to BuildMaster 2024:
1. Remove old ( Legacy SDK) extensions as required 
2. If you are using the `SqlServer 1.0.0` extension, it will not load; delete it and download `SqlServer 2.0.0`
3.  Upgrade/install  extensions as needed

:::(Warning) (Backup Before Upgrading)
You should make sure your [installation is backed-up](/docs/installation/backing-up-restoring).
:::

While you can upgrade from BuildMaster 6.1 and later to BuildMaster 2024 (i.e. there is no need to install intermediate versions), you can only rollback to BuildMaster 2023 without restoring your database.

## Rolling Back

:::(Error) (Database Restore may be required)
If you want to rollback to BuildMaster 2022 or earlier, you will need to restore the instance to the previous state. See the [Backing Up & Restoring](/docs/installation/backing-up-restoring) documentation to learn more.
:::

However, if you need to rollback to BuildMaster 2023 you can do so without restoring the database by simply using the Inedo Hub. While there are database schema changes, they are all backwards-compatible with BuildMaster 2023, which means you can safely rollback your BuildMaster installation if there's a showstopper bug, and then upgrade later.