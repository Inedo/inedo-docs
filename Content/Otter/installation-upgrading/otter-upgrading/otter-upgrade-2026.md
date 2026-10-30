---
title: "Upgrading to Otter 2026"
nav-title: "Upgrading to Otter 2026"
order: 1
url-slug: "otter-upgrade-2026"
---

Otter 2026 is a major update, and this article provides information about what will change, the impact to your instance, and how to mitigate risk during upgrade.

## Planning for Your Upgrade

Many of the changes were additive features, platform updates (library upgrades, etc.), and UI changes.

### Upgrading from Otter 2025
:::(Info)
If you're upgrading from Otter 2025, upgrading to Otter 2026 should be relatively easy.
:::

If there are issues, you can rollback to Otter 2025 without restoring your database.

### Upgrading from Older Versions

If you're currently using Otter 3.0, Otter 2022, Otter 2023, Otter 2024, or Otter 2025 we recommend directly upgrading to Otter 2026. While it won't *hurt* to do incremental upgrades, there's almost never a benefit. However, if there are issues, you will need to restore your database before rolling back.

:::(Info) (IIS No Longer Supported on Windows)
Windows IIS support ended in Otter 2025, and if you're are still using IIS you'll need to migrate before upgrading. See [Configuration Change: IIS Hosting on Windows](/docs/otter-upgrade-2025#iis-hosting-on-windows).
:::

Please read the upgrade notes from each version to learn what changed and how to mitigate risks. To summarize:
* [Otter 2025](/docs/otter-upgrade-2025); platform modernization (dropped IIS support, hosting changes); removed deprecated features
* [Otter 2024](/docs/otter-upgrade-2024); Git library improvements, HTTP/S improvements, library updates
* [Otter 2023](/docs/otter-upgrade-2023); additive OtterScript changes, library changes, platform update (.NET6 -> .NET8)
* [Otter 2022](/docs/otter-upgrade-2022); expanded Scripting Language Support, new OtterScript editor, PowerShell operation syntax changes, platform change (.NET Framework -> .NET6)
* [Otter 3.0](/docs/otter-upgrading-to-3-0); lots of changes, primarily refocusing product around primary use cases

You can safely downgrade to Otter 2025 without rolling back the database.

## Improvements & Features in Otter 2026


### PostgreSQL Support Added

As part of our multi-year journey of [saying goodbye to SQL Server](https://blog.inedo.com/inedo/so-long-sql-server-thanks-for-all-the-fetch/), we've now added PostgreSQL support to Otter 2026. 

New installations of Otter 2026 will default to PostgreSQL and you can switch from SQL Server to PostgreSQL using the [Export-based Migration process](/docs/installation/postgresql#migrating-from-sqlserver-all).

### Changes & Improvements
* **Platform Upgrade (.NET8 to .NET10)**
* **"Notifications" Feature**; provides in-application notifications for failed scheduled jobs, new upgrades, and similar functionality

## Upgrade Process
You should generally perform the upgrade using the same method you used to install.

* On Windows, the most common installation method is using the internet-connected [Inedo Hub](/docs/installation/windows/inedo-hub); see  [HOWTO: Upgrade or Downgrade with the Inedo Hub](/docs/installation/windows/howto-upgrade-downgrade) for more details on how to upgrade.
* On Linux, the most common installation method is using our Docker container; see [Upgrading Docker Containers](/docs/installation/linux/installation-upgrading-docker-containers) for more details.

However, there are other installation options available, including [offline installation](/docs/installation/windows/inedo-hub/offline), [cluster installation](/docs/installation/high-availability-load-balancing/high-availability-load-balancing), and even [manual installation](/docs/installation/windows/manual-installation). If you want to change installation methods, the easiest way is to simply uninstall (by following the process in reverse) and install using the new method.

:::(Warning) (Backup Before Upgrading)
You should make sure your [installation is backed-up](/docs/installation/backing-up-restoring).
:::

While you can upgrade from any version to Otter 2026 (i.e. there is no need to install intermediate versions), you can only rollback to Otter 2025 without restoring your database.

## Rolling Back

:::(Error) (Database Restore may be required)
If you want to rollback to Otter 2024 or earlier, you will need to restore the instance to the previous state. See the [Backing Up & Restoring](/docs/installation/backing-up-restoring) documentation to learn more.
:::

However, if you need to rollback to Otter 2025 you can do so without restoring the database by simply using the Inedo Hub. While there are database schema changes, they are all backwards-compatible with Otter 2025, which means you can safely rollback your Otter installation if there's a showstopper bug, and then upgrade later.
