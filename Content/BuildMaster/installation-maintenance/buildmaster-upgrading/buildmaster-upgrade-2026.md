---
title: "Upgrading to BuildMaster 2026"
nav-title: "BuildMaster 2026"
order: 1
url-slug: "buildmaster-upgrade-2026"
---

BuildMaster 2026 is a major update, and this article provides information about what will change, the impact to your instance, and how to mitigate risk during upgrade.

## Planning for Your Upgrade

Many of the changes were additive features, platform updates (library upgrades, etc.), and UI changes.

### Upgrading from BuildMaster 2025
:::(Info)
If you're upgrading from BuildMaster2025, upgrading to BuildMaster 2026 should be relatively easy.
:::

If there are issues, you can rollback to BuildMaster 2025 without restoring your database.

### Upgrading from Older Versions

If you're currently using BuildMaster 6.2, BuildMaster 7.0, BuildMaster 2022, BuildMaster 2023, or BuildMaster 2024, we recommend directly upgrading to BuildMaster 2026. While it won't *hurt* to do incremental upgrades, there's almost never a benefit. However, if there are issues, you will need to restore your database before rolling back.

:::(Info) (IIS Not Longer Supported on Windows)
Windows IIS support ended in BuildMaster 2025, and if you're are still using IIS you'll need to migrate before upgrading. See [Configuration Change: IIS Hosting on Windows](/docs/buildmaster-upgrade-2025#configuration-change-iis-hosting-on-windows).
:::

Please read the upgrade notes from each version to learn what changed and how to mitigate risks. To summarize:
* [BuildMaster 2025](/docs/buildmaster-upgrade-2025); platform modernization (dropped IIS support, hosting changes); removed deprecated features, rewrote Git support 
* [BuildMaster 2024](/docs/buildmaster-upgrade-2024); general platform upgrades and pipeline editor and UI improvements
* [BuildMaster 2023](/docs/buildmaster-upgrade-2023); reworked Docker integration and Issue Tracking integration. 
* [BuildMaster 2022](/docs/buildmaster-upgrade-2022); reworked Git integration, made pipeline changes, and PowerShell operation changes
* [BuildMaster 7.0](/docs/buildmaster-upgrading-to-v7); very little that changed from an upgrade standpoint. We mostly standardized libraries, adding Linux support, high-availability, etc.


### Upgrading from BuildMaster 6.1 or Earlier
:::(Warning)
If you're upgrading from BuildMaster 6.1 or earlier, upgrading to BuildMaster 2026 may be challenging.
:::
In BuildMaster 6.2, a significant number of [Legacy Features](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features) were removed from the product. See [Upgrading to BuildMaster 6.2](/docs/buildmaster-upgrading-to-6-2) to learn more about what else changed in that release. Alternatively, you may want to consider an [application-by-application migration approach](/docs/buildmaster/installation-maintenance/buildmaster-migrating-instance-to-new-server#migrating-applicationbyapplication).

You wont be able to upgrade to BuildMaster 2026 until the [Legacy Feature Detector](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features#legacy-feature-detector) has reported no legacy features. This is only available in the latest versions of BuildMaster 6.1, which means you will need to [upgrade to BuildMaster 6.1](/docs/buildmaster-upgrading-to-6-1) first.

Because upgrading from BuildMaster 6.1 is more involved than previous upgrades, special support for it has been included with paid licenses. Simply use the [Submit Ticket Form](https://my.inedo.com/tickets/new), put `2026 UPGRADE` in the "How can we help" section, and include the [Legacy Features Dashboard Report Logs](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features#legacy-feature-detector) in the text body of the ticket.

## Improvements & Features in BuildMaster 2026

### PostgreSQL Support Added

As part of our multi-year journey of [saying goodbye to SQL Server](https://blog.inedo.com/inedo/so-long-sql-server-thanks-for-all-the-fetch/), we've now added PostgreSQL support to BuildMaster 2026. 

New installations of BuildMaster 2026 will default to PostgreSQL and you can switch from SQL Server to PostgreSQL using the [Export-based Migration process](/docs/installation/postgresql#migrating-from-sqlserver-bm).

### 

We've rebuilt the UI for the release calendars and made it more more prominent on the navigation. 

### Changes & Improvements
* **Release Date Overhaul**; the "release dates" feature was overhauled to default to adding a target date and allow for other dates to be specified via the pipeline
* **Release Calendar Overhaul**; we've rebuilt the release calendar UI and made it more prominent in the navigation
* **Iterative Releases**; a field was added on pipelines to allow a release to automatically iterate after a fixed number of days for clearer display on calendars 
* **Platform Upgrade (.NET8 to .NET10)**
* **"Notifications" Feature**; provides in-application notifications for new upgrades and similar functionality

## Upgrade Process
You should generally perform the upgrade using the same method you used to install.

* On Windows, the most common installation method is using the internet-connected [Inedo Hub](/docs/installation/windows/inedo-hub); see  [HOWTO: Upgrade or Downgrade with the Inedo Hub](/docs/installation/windows/howto-upgrade-downgrade) for more details on how to upgrade.
* On Linux, the most common installation method is using our Docker container; see [Upgrading Docker Containers](/docs/installation/linux/installation-upgrading-docker-containers) for more details.

However, there are other installation options available, including [offline installation](/docs/installation/windows/inedo-hub/offline), [cluster installation](/docs/installation/high-availability-load-balancing/high-availability-load-balancing), and even [manual installation](/docs/installation/windows/manual-installation). If you want to change installation methods, the easiest way is to simply uninstall (by following the process in reverse) and install using the new method.

If you're upgrading from BuildMaster 6.2 or earlier, it's possible BuildMaster was installed with the [legacy installer  (github.com)](https://github.com/Inedo/inedo-docs/blob/c82fd2881e2f1d0c36e77bc8b8b48e2a2c7b75a9/Content/installation/windows/installation-legacy-traditional-installer.md), The Inedo Hub should be able to upgrade these installations, but you may need to uninstall and then reinstall using the Inedo Hub. 

If you are upgrading from BuildMaster 6.1, you will need to perform some additional steps after upgrading to BuildMaster 2026:
1. Remove old ( Legacy SDK) extensions as required 
2. If you are using the `SqlServer 1.0.0` extension, it will not load; delete it and download `SqlServer 2.0.0`
3.  Upgrade/install  extensions as needed

:::(Warning) (Backup Before Upgrading)
You should make sure your [installation is backed-up](/docs/installation/backing-up-restoring).
:::

While you can upgrade from BuildMaster 6.1 and later to BuildMaster 2026 (i.e. there is no need to install intermediate versions), you can only rollback to BuildMaster 2024 without restoring your database.

## Rolling Back

:::(Error) (Database Restore may be required)
If you want to rollback to BuildMaster 2024 or earlier, you will need to restore the instance to the previous state. See the [Backing Up & Restoring](/docs/installation/backing-up-restoring) documentation to learn more.
:::

However, if you need to rollback to BuildMaster 2025 you can do so without restoring the database by simply using the Inedo Hub. While there are database schema changes, they are all backwards-compatible with BuildMaster 2024, which means you can safely rollback your BuildMaster installation if there's a showstopper bug, and then upgrade later.