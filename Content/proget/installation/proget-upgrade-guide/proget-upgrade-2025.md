---
title: "Upgrading to ProGet 2025"
order: 0
url-slug: "proget-upgrade-2025"
---

ProGet 2025 is a major update, and this article provides information about what will change, the impact to your instance, and how to mitigate risk during upgrade.


## Planning for Your Upgrade

ProGet 2025 is a major upgrade, and many of the changes were additive features (available as "preview" features in ProGet 2024), configuration changes (Windows Service architecture, Docker base image), platform updates (library upgrades, etc.), and UI changes.

### Upgrading from ProGet 2024
:::(Info)
If you're upgrading from ProGet 2024, upgrading to ProGet 2025 should be relatively easy, unless you are still using IIS. See [Configuration Change: IIS Hosting on Windows](#configuration-change-iis-hosting-on-windows).
:::

If there are issues, you can rollback to ProGet 2024 without restoring your database.

### Upgrading from Older Versions

If you're currently using ProGet 5.2, ProGet 5.3, ProGet 6.0, ProGet 2022, or ProGet 2023 we recommend directly upgrading to ProGet 2025. While it won't *hurt* to do incremental upgrades, there's almost never a benefit. However, if there are issues, you will need to restore your database before rolling back.

:::(Warn) (Required Data Migration & Disabled Retention Rules for ProGet 2022 & Earlier)
After upgrading, ProGet will automatically initiate a data migration; see [New Package Indexing System in ProGet 2023](/docs/proget-upgrade-2024#new-package-indexing-system-in-proget-2023) to learn about this process. 

The migration should be relatively quick and require no user intervention. After migration is complete, you will need to re-enable retention rules on your feeds. 
:::

Please read the upgrade notes from each version to learn what changed and how to mitigate risks. To summarize:
    
In [ProGet 2024](/docs/proget-upgrade-2024), many of the changes were in ProGet's SCA features like Vulnerability Scanning, License Detection, Assessments, SBOMS, and Projects/Builds. Webhooks & Notifiers were also redesigned.

In [ProGet 2023](/docs/proget-upgrade-2023), in addition to the new packaging indexing system, there were several changes surrounding vulnerability scanning. One breaking change was that Docker images will no longer have the `library/` namespace automatically appended on push.

In [ProGet 2022](/docs/proget-upgrade-2022), the hosting platform changed to .NET6 and users initially reported performance issues, but these should be mostly fixed in maintenance releases. In addition, the [Package Consumers](/docs/proget/installation/proget-old-versions-migration) feature was also replaced in favor of [SCA Projects and Releases](/docs/proget/sca/builds).
    
In [ProGet 6.0](/docs/proget-6-0-upgrade-notes), due API Keys changes, some users reported that some [API Keys](/docs/proget/api/apikeys) needed to edited or deleted and recreated to fix permissions issues.

In [ProGet 5.3](/docs/proget-5-3-upgrade-notes), legacy NuGet ("quirks") feeds were removed. You will need to migrate all of these feeds before upgrading.


### Upgrading from ProGet 5.1 or Earlier
:::(Error)
If you're upgrading from ProGet 5.1 or earlier, you should upgrade to ProGet 5.2 first. Upgrading directly is not recommended.
:::

We recommend upgrade to latest ProGet 5.2, then latest ProGet 2025. See: [5.3 notes](/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrading-to-proget-5-2), [ v4 notes](/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrade-notes-for-proget-v4), [v2/v3 notes](/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrading-from-proget-v2-and-v3)

## New Features in ProGet 2025

### New Feature: PostgreSQL Database Backend

Starting with ProGet 2025, ProGet will be [shifting the database backend from SQL Server to PostgreSQL](https://blog.inedo.com/inedo/so-long-sql-server-thanks-for-all-the-fetch/). However, the SQL Server backend will be supported through at least ProGet 2026, which means that migrating to the new backend is optional for now.

:::(Info)
The PostgreSQL database backend is a side-by-side feature and adds no real risk until you use it. 
:::

The ability to migrate to PostgreSQL was [first introduced as a technology preview in ProGet 2024.33](https://blog.inedo.com/inedo/postgresql-technical-preview/), refined in subsequent maintenance releases, and will likely continue to be refined through ProGet 2025.


### New Feature: Feed Integrity Checks

ProGet 2025 can routinely perform an integrity check on feeds to prevent errors or unexpected behaviors when accessing content. This involves inspecting each package file on disk at the end of each feed's FeedCleanup Scheduled Job and, if any issues are discovered, displaying a notice to administrators that the feed failed an integrity check. Administrators can then re-index the problematic feed to resolve these issues.

:::(Info)
Feed Integrity Checks were added as a preview feature in ProGet 2024.36. Enabling the preview feature will help mitigate risk of higher-than-usual I/O activity during feed cleanup.
:::

ProGet 2025 will allow you disable integrity checks on a feed-by-feed basis.


## Other Improvements & Notable Changes

### Style / Font Tweaks

We made some minor style changes, most notably the background colors and fonts.

### Reduced Features for Legacy Maven & Legacy Debian Feeds

As part of modernization and reducing technical debt in ProGet, it is no longer makes sense to support certain features for Legacy Maven and Legacy Debian feeds: replication, retention, license detection, vulnerability scanning, policies/compliance, and download blocking. They will also have a reduced browsing experience in the UI, but will operate as they did before from the API.

Normal (i.e. non-legacy) Maven and Debian feeds do not have these limitations and support all of these features. You will be able to migrate in ProGet 2025, but we recommend doing so earlier.

### Configuration Change: Windows Service Architecture

In ProGet 2025, there will just be a single Windows service (`INEDOPROGETSVC`) that will be used to manage the Web Server and background jobs. ProGet 2024 and earlier versions used two Windows Services (`INEDOPROGETWEBSVC` and `INEDOPROGETSVC`).

:::(Info)
This was introduced as the "Optimized Service Mode" preview feature in ProGet 2024.33. Enabling the preview feature will help mitigate risk of the new configuration behaving differently in ProGet 2025.
:::

Note that the `INEDOPROGETWEBSVC` will still exist, but only to make it easier to rollback to ProGet 2024 if needed. It will likely be deleted in ProGet 2026, but you can remove it earlier if you'd like. It does nothing except wait for a `Stop` command to terminate.


### Configuration Change: IIS Hosting on Windows

ProGet 2025 can no longer be run as an IIS-managed Site and Application Pool. Following Microsoft's guidance to [no longer use IIS for modern .NET applications](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel), we stopped recommend this configuration as of ProGet 2022. However, it was still possible to run in IIS instead of using the `INEDOPROGETWEBSVC` service. 

:::(Error)
ProGet 2025 will not work in IIS without manual configuration changes.
:::

Prior to upgrading to ProGet 2025, we recommend [switching to the Integrated Web Server](/docs/installation/windows/web/howto-switch-to-integrated-web-server-from-iis). If you still want to use IIS, then you will need to [reconfigure IIS to act as a reverse-proxy](/docs/installation/windows/web/howto-use-iis-as-reverse-proxy). 

### Configuration Change: SQL Server Permissions on Windows

ProGet 2025 will now perform database schema updates when the Windows service starts, making it easier to detect errors. In earlier versions of ProGet, these updates were performed during the upgrade/downgrade process, in the context of the user running the upgrade.

:::(Error)
ProGet 2025 requires that the `db_owner` role is granted to application user
:::

If you installed ProGet 2024.30 or later, the Inedo Hub installer automatically attempted to grant all users with the `ProGetUser_Role` the `db_owner role`. 

To ensure the application user has the appropriate permissions before upgrading, [enable Enhanced Database Monitoring in ProGet 2024](/docs/installation/sql-server#enhanced-database-monitoring) prior to upgrading.

### Various Changes & Improvements
* **SCA tweaks** including CSS/UI changes, adding license text field in SBOM exports for custom license, linking build URL back to system and more, deprecation warnings, and SBOM 1.6 support
* **npm Repackage changes**; very minor, but the `latest` tag will now be moved to the newer package
* **Rename Marvin "Classic" to "Legacy"** and update the `Maven` type in the API to create the newer Maven feeds
* **Withdrawn Vulnerabilities**; unless they've been assessed, these will be deleted from the local database after each update of the vulnerability database (default nightly)
* **Pause/disable individual replication tasks**; this will likely not add any risk
* **Add Certificates folder to Package/Data Directory**; minor change to Docker image
* Debian Feeds now Return empty InRelease file for Empty Feed

## Upgrade Process
You should generally perform the upgrade using the same method you used to install.

* On Windows, the most common installation method is using the internet-connected [Inedo Hub](/docs/installation/windows/inedo-hub); see  [HOWTO: Upgrade or Downgrade with the Inedo Hub](/docs/installation/windows/howto-upgrade-downgrade) for more details on how to upgrade.
* On Linux, the most common installation method is using our Docker container; see [Upgrading Docker Containers](/docs/installation/linux/installation-upgrading-docker-containers) for more details.

However, there are other installation options available, including [offline installation](/docs/installation/windows/inedo-hub/offline), [cluster installation](/docs/installation/high-availability-load-balancing/high-availability-load-balancing), and even [manual installation](/docs/installation/windows/manual-installation). If you want to change installation methods, the easiest way is to simply uninstall (by following the process in reverse) and install using the new method.

If you're upgrading from ProGet 5.3 or earlier, it's possible ProGet was installed with the [legacy installer (github.com)](https://github.com/Inedo/inedo-docs/blob/c82fd2881e2f1d0c36e77bc8b8b48e2a2c7b75a9/Content/installation/windows/installation-legacy-traditional-installer.md), The Inedo Hub should be able to upgrade these installations, but you may need to uninstall and then reinstall using the Inedo Hub. 

:::(Warning) (Backup Before Upgrading)
You should make sure your [installation is backed-up](/docs/installation/backing-up-restoring).
:::

While you can upgrade from ProGet 5.0 and later to ProGet 2025 (i.e. there is no need to install intermediate versions), you can only rollback to ProGet 2024 without restoring your database.

## Rolling Back

:::(Error) (Database Restore may be required)
If you want to rollback to ProGet 2023 or earlier, you will need to restore the instance to the previous state. See the [Backing Up & Restoring](/docs/installation/backing-up-restoring) documentation to learn more.
:::

However, if you need to rollback to ProGet 2024, you can do so without restoring the database by simply using the Inedo Hub. While there are database schema changes, they are all backwards-compatible with ProGet 2024, which means you can safely rollback your ProGet installation if there's a showstopper bug, and then upgrade later.
