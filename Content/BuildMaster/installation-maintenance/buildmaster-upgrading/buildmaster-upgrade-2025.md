---
title: "Upgrading to BuildMaster 2025"
nav-title: "BuildMaster 2025"
order: 1
url-slug: "buildmaster-upgrade-2025"
---

BuildMaster 2025 is a major update, and this article provides information about what will change, the impact to your instance, and how to mitigate risk during upgrade.

## Planning for Your Upgrade

Many of the changes were additive features, configuration changes (Windows Service architecture), platform updates (library upgrades, etc.), and UI changes.

### Upgrading from BuildMaster 2024
:::(Info)
If you're upgrading from BuildMaster 2024, upgrading to BuildMaster 2025 should be relatively easy, unless you are still using IIS. See [Configuration Change: IIS Hosting on Windows](#configuration-change-iis-hosting-on-windows).
:::

If there are issues, you can rollback to BuildMaster 2024 without restoring your database.

### Upgrading from Older Versions

If you're currently using BuildMaster 6.2, BuildMaster 7.0, BuildMaster 2022, or BuildMaster 2023, we recommend directly upgrading to BuildMaster 2024. While it won't *hurt* to do incremental upgrades, there's almost never a benefit. However, if there are issues, you will need to restore your database before rolling back.

Please read the upgrade notes from each version to learn what changed and how to mitigate risks. To summarize:

* [BuildMaster 2024](/docs/buildmaster-upgrade-2024); general platform upgrades and pipeline editor and UI improvements
* [BuildMaster 2023](/docs/buildmaster-upgrade-2023); reworked Docker integration and Issue Tracking integration. 
* [BuildMaster 2022](/docs/buildmaster-upgrade-2022); reworked Git integration, made pipeline changes, and PowerShell operation changes
* [BuildMaster 7.0](/docs/buildmaster-upgrading-to-v7); very little that changed from an upgrade standpoint. We mostly standardized libraries, adding Linux support, high-availability, etc.


### Upgrading from BuildMaster 6.1 or Earlier
:::(Warning)
If you're upgrading from BuildMaster 6.1 or earlier, upgrading to BuildMaster 2025 may be challenging.
:::
In BuildMaster 6.2, a significant number of [Legacy Features](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features) were removed from the product. See [Upgrading to BuildMaster 6.2](/docs/buildmaster-upgrading-to-6-2) to learn more about what else changed in that release. Alternatively, you may want to consider an [application-by-application migration approach](/docs/buildmaster/installation-maintenance/buildmaster-migrating-instance-to-new-server#migrating-applicationbyapplication).

You wont be able to upgrade to BuildMaster 2025 until the [Legacy Feature Detector](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features#legacy-feature-detector) has reported no legacy features. This is only available in the latest versions of BuildMaster 6.1, which means you will need to [upgrade to BuildMaster 6.1](/docs/buildmaster-upgrading-to-6-1) first.

Because upgrading from BuildMaster 6.1 is more involved than previous upgrades, special support for it has been included with paid licenses. Simply use the [Submit Ticket Form](https://my.inedo.com/tickets/new), put `2025 UPGRADE` in the "How can we help" section, and include the [Legacy Features Dashboard Report Logs](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features#legacy-feature-detector) in the text body of the ticket.

## Improvements & Features in BuildMaster 2025

### New Git Library

Due to ongoing performance, troubleshooting, and configuration issues with [libgit2](https://libgit2.org/), we built our own Git library from the ground-up. 

This library will be enabled by default on newly-created applications, but existing applications will need to opt-in to use the new library as to reduce the risk of regressions. This can be done on the application's "Settings" page. 

In a future version of BuildMaster, we will completely remove `libgit2` - so if you have a particularly large or complex repository, we suggest giving it a shot now.

### Application Slugs & Friendly URLs

Applications can now be configured with a "slug" to create friendly URLs that include the application name, release, and build number.

For example, when `my-application` is configured as the "slug" for "My Application (ID=106)", the URLs will be
`/applications/my-application/builds/4.2.0/3/build` instead of `/applications/106/builds/build?buildId=13585`

This only impacts the web user interface.

### Database Code Cleanup & Preparation

We are planning to [shift BuildMaster's database backend from SQL Server to PostgreSQL](https://blog.inedo.com/inedo/so-long-sql-server-thanks-for-all-the-fetch/) in BuildMaster 2026 or later.

To make it easier to handle this in the future, we cleaned up existing SQL Server code. We also identified several inefficient queries and refactored them.

### Discontinued Features in BuildMaster 2025

As discussed in our [Legacy (Deprecated) Features](/docs/buildmaster/installation-maintenance/buildmaster-legacy) guide, several features were removed:

* [Deployables](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-applications-concepts-deployables) (obsolete since the introduction of OtterScript and hidden from the UI since BuildMaster 2022)
* [Release Templates](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-applications-releases-templates) (duplicative feature since the introduction of [Pipeline Variable Prompts](/docs/buildmaster/otterscript-execution-engine/buildmaster-variables))
* [GitHub & GitLab Webhook Monitors](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-webhooks) (replaced with [Git Resource Monitors](/docs/buildmaster/administration/buildmaster-resource-monitors))
* [Issue Sources](/docs/buildmaster/modeling-your-applications/buildmaster-applications-issue-tracking#buildmaster-2022-and-earlier)  (replaced with [Issue Tracker Projects](/docs/buildmaster/modeling-your-applications/buildmaster-applications-issue-tracking))

Note that the underlying database tables were not dropped, which means rolling back will work fine if you somehow were using these features.

### Style / Font Tweaks

We made some minor style changes, most notably the background colors and fonts.

## Configuration Changes (Windows)

### Windows Service Architecture

In BuildMaster 2025, there will just be a single Windows service (`INEDOBMSVC`) that will be used to manage the Web Server and background jobs. Buildmaster 2024 and earlier versions used two Windows Services (`INEDOBMWEBSVC` and `INEDOBMSVC`).

Note that the `INEDOBMWEBSVC` will still exist, but only to make it easier to rollback to BuildMaster 2024 if needed. It will likely be deleted in BuildMaster 2026, but you can remove it earlier if you'd like. It does nothing except wait for a `Stop` command to terminate.

### IIS Hosting on Windows

BuildMaster 2025 can no longer be run as an IIS-managed Site and Application Pool. Following Microsoft's guidance to [no longer use IIS for modern .NET applications](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel), we stopped recommend this configuration as of BuildMaster 2022. However, it was still possible to run in IIS instead of using the `INEDOBMWEBSVC` service. 

:::(Error)
BuildMaster 2025 will not work in IIS without manual configuration changes.
:::

Prior to upgrading to BuildMaster 2025, we recommend [switching to the Integrated Web Server](/docs/installation/windows/web/howto-switch-to-integrated-web-server-from-iis). If you still want to use IIS, then you will need to [reconfigure IIS to act as a reverse-proxy](/docs/installation/windows/web/howto-use-iis-as-reverse-proxy). 

### SQL Server Permissions on Windows

BuildMaster 2025 will now perform database schema updates when the Windows service starts, making it easier to detect errors. In earlier versions of BuildMaster, these updates were performed during the upgrade/downgrade process, in the context of the user running the upgrade.

:::(Error)
BuildMaster 2025 requires that the `db_owner` role is granted to application user
:::

The Inedo Hub installer automatically attempted to grant all users with the `BuildMasterUser_Role` the `db_owner role`. 

## Upgrade Process
You should generally perform the upgrade using the same method you used to install.

* On Windows, the most common installation method is using the internet-connected [Inedo Hub](/docs/installation/windows/inedo-hub); see  [HOWTO: Upgrade or Downgrade with the Inedo Hub](/docs/installation/windows/howto-upgrade-downgrade) for more details on how to upgrade.
* On Linux, the most common installation method is using our Docker container; see [Upgrading Docker Containers](/docs/installation/linux/installation-upgrading-docker-containers) for more details.

However, there are other installation options available, including [offline installation](/docs/installation/windows/inedo-hub/offline), [cluster installation](/docs/installation/high-availability-load-balancing/high-availability-load-balancing), and even [manual installation](/docs/installation/windows/manual-installation). If you want to change installation methods, the easiest way is to simply uninstall (by following the process in reverse) and install using the new method.

If you're upgrading from BuildMaster 6.2 or earlier, it's possible BuildMaster was installed with the [legacy installer  (github.com)](https://github.com/Inedo/inedo-docs/blob/c82fd2881e2f1d0c36e77bc8b8b48e2a2c7b75a9/Content/installation/windows/installation-legacy-traditional-installer.md), The Inedo Hub should be able to upgrade these installations, but you may need to uninstall and then reinstall using the Inedo Hub. 

If you are upgrading from BuildMaster 6.1, you will need to perform some additional steps after upgrading to BuildMaster 2025:
1. Remove old ( Legacy SDK) extensions as required 
2. If you are using the `SqlServer 1.0.0` extension, it will not load; delete it and download `SqlServer 2.0.0`
3.  Upgrade/install  extensions as needed

:::(Warning) (Backup Before Upgrading)
You should make sure your [installation is backed-up](/docs/installation/backing-up-restoring).
:::

While you can upgrade from BuildMaster 6.1 and later to BuildMaster 2025 (i.e. there is no need to install intermediate versions), you can only rollback to BuildMaster 2024 without restoring your database.

## Rolling Back

:::(Error) (Database Restore may be required)
If you want to rollback to BuildMaster 2023 or earlier, you will need to restore the instance to the previous state. See the [Backing Up & Restoring](/docs/installation/backing-up-restoring) documentation to learn more.
:::

However, if you need to rollback to BuildMaster 2025 you can do so without restoring the database by simply using the Inedo Hub. While there are database schema changes, they are all backwards-compatible with BuildMaster 2023, which means you can safely rollback your BuildMaster installation if there's a showstopper bug, and then upgrade later.