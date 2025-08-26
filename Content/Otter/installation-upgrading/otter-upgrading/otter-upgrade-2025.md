---
title: "Upgrading to Otter 2025"
order: 1
url-slug: "otter-upgrade-2025"
---

Otter 2025 is a major upgrade, and this article provides information about what changed, the impact to your instance, and how to mitigate risk during upgrade.

## Planning for Your Upgrade

Many of the changes were additive features, configuration changes (Windows Service architecture), platform updates (library upgrades, etc.), and UI changes.

### Upgrading from Otter 2024
:::(Info)
If you're upgrading from Otter 2024, upgrading to Otter 2025 should be relatively easy, unless you are still using IIS. See [Configuration Change: IIS Hosting on Windows](#configuration-change-iis-hosting-on-windows).
:::

If there are issues, you can rollback to Otter 2024 without restoring your database.

### Upgrading from Older Versions

If you're currently using Otter 3.0, Otter 2022, or Otter 2023 we recommend directly upgrading to Otter 2025. While it won't *hurt* to do incremental upgrades, there's almost never a benefit. However, if there are issues, you will need to restore your database before rolling back.

Please read the upgrade notes from each version to learn what changed and how to mitigate risks. To summarize:

* [Otter 2024](/docs/otter-upgrade-2024); Git library improvements, HTTP/S improvements, library updates
* [Otter 2023](/docs/otter-upgrade-2023); additive OtterScript changes, library changes, platform update (.NET6 -> .NET8)
* [Otter 2022](/docs/otter-upgrade-2022); expanded Scripting Language Support, new OtterScript editor, PowerShell operation syntax changes, platform change (.NET Framework -> .NET6)
* [Otter 3.0](/docs/otter-upgrading-to-3-0); lots of changes, primarily refocusing product around primary use cases

You can safely downgrade to Otter 2024 without rolling back the database.

## Improvements & Features in Otter 2025

### Database Code Cleanup & Preparation

We are planning to [shift Otter's database backend from SQL Server to PostgreSQL](https://blog.inedo.com/inedo/so-long-sql-server-thanks-for-all-the-fetch/) in Otter 2026 or later.

To make it easier to handle this in the future, we cleaned up existing SQL Server code. We also identified several inefficient queries and refactored them.

### Style / Font Tweaks

We made some minor style changes, most notably the background colors and fonts.

## Configuration Changes (Windows)

### Windows Service Architecture

In Otter 2025, there will just be a single Windows service (`INEDOOTTERSVC`) that will be used to manage the Web Server and background jobs. Otter 2024 and earlier versions used two Windows Services (`INEDOOTTERWEBSVC` and `INEDOOTTERSVC`).

Note that the `INEDOOTTERWEBSVC` will still exist, but only to make it easier to rollback to Otter 2024 if needed. It will likely be deleted in Otter 2026, but you can remove it earlier if you'd like. It does nothing except wait for a `Stop` command to terminate.

### IIS Hosting on Windows

Otter 2025 can no longer be run as an IIS-managed Site and Application Pool. Following Microsoft's guidance to [no longer use IIS for modern .NET applications](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel), we stopped recommend this configuration as of Otter 2022. However, it was still possible to run in IIS instead of using the `INEDOOTTERWEBSVC` service. 

:::(Error)
Otter 2025 will not work in IIS without manual configuration changes.
:::

Prior to upgrading to Otter 2025, we recommend [switching to the Integrated Web Server](/docs/installation/windows/web/howto-switch-to-integrated-web-server-from-iis). If you still want to use IIS, then you will need to [reconfigure IIS to act as a reverse-proxy](/docs/installation/windows/web/howto-use-iis-as-reverse-proxy). 

### SQL Server Permissions on Windows

Otter 2025 will now perform database schema updates when the Windows service starts, making it easier to detect errors. In earlier versions of Otter, these updates were performed during the upgrade/downgrade process, in the context of the user running the upgrade.

:::(Error)
Otter 2025 requires that the `db_owner` role is granted to application user
:::

The Inedo Hub installer automatically attempted to grant all users with the `OtterUser_Role` the `db_owner role`. 

## Upgrade Process
You should generally perform the upgrade using the same method you used to install.

* On Windows, the most common installation method is using the internet-connected [Inedo Hub](/docs/installation/windows/inedo-hub); see  [HOWTO: Upgrade or Downgrade with the Inedo Hub](/docs/installation/windows/howto-upgrade-downgrade) for more details on how to upgrade.
* On Linux, the most common installation method is using our Docker container; see [Upgrading Docker Containers](/docs/installation/linux/installation-upgrading-docker-containers) for more details.

However, there are other installation options available, including [offline installation](/docs/installation/windows/inedo-hub/offline), [cluster installation](/docs/installation/high-availability-load-balancing/high-availability-load-balancing), and even [manual installation](/docs/installation/windows/manual-installation). If you want to change installation methods, the easiest way is to simply uninstall (by following the process in reverse) and install using the new method.

:::(Warning) (Backup Before Upgrading)
You should make sure your [installation is backed-up](/docs/installation/backing-up-restoring).
:::

While you can upgrade from any version to Otter 2025 (i.e. there is no need to install intermediate versions), you can only rollback to Otter 2024 without restoring your database.

## Rolling Back

:::(Error) (Database Restore may be required)
If you want to rollback to Otter 2023 or earlier, you will need to restore the instance to the previous state. See the [Backing Up & Restoring](/docs/installation/backing-up-restoring) documentation to learn more.
:::

However, if you need to rollback to Otter 2025 you can do so without restoring the database by simply using the Inedo Hub. While there are database schema changes, they are all backwards-compatible with Otter 2023, which means you can safely rollback your Otter installation if there's a showstopper bug, and then upgrade later.