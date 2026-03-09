---
title: "Upgrading to ProGet 2026"
order: 0
url-slug: "proget-upgrade-2026"
---

ProGet 2026 is a major update, and this article provides information about what will change, the impact to your instance, and how to mitigate risk during upgrade.


## Planning for Your Upgrade

ProGet 2026 is a major upgrade, and many of the changes were additive features (available as "preview" features in ProGet 2025), configuration changes (Windows Service architecture, Docker base image), platform updates (library upgrades, etc.), and UI changes.

### Upgrading from ProGet 2025
:::(Info)
If you're upgrading from ProGet 2025, upgrading to ProGet 2026 should be relatively easy.
:::

If there are issues, you can rollback to ProGet 2025 without restoring your database.

### Upgrading from Older Versions

If you're currently using ProGet 5.2, ProGet 5.3, ProGet 6.0, ProGet 2022, ProGet 2023, or ProGet 2024 we recommend directly upgrading to ProGet 2026. While it won't *hurt* to do incremental upgrades, there's almost never a benefit. However, if there are issues, you will need to restore your database before rolling back.

:::(Warn) (IIS Not Longer Supported on Windows)
Windows IIS support ended in ProGet 2025, and if you are still using IIS you'll need to migrate before upgrading. See [Configuration Change: IIS Hosting on Windows](#configuration-change-iis-hosting-on-windows).
:::

:::(Warn) (Required Data Migration & Disabled Retention Rules for ProGet 2022 & Earlier)
After upgrading, ProGet will automatically initiate a data migration; see [New Package Indexing System in ProGet 2023](/docs/proget-upgrade-2025#new-package-indexing-system-in-proget-2023) to learn about this process. 

The migration should be relatively quick and require no user intervention. After migration is complete, you will need to re-enable retention rules on your feeds. 
:::

Please read the upgrade notes from each version to learn what changed and how to mitigate risks. To summarize:
    
In [ProGet 2025](/docs/proget-upgrade-2025), PostgreSQL support was added and IIS support was removed.

In [ProGet 2024](/docs/proget-upgrade-2024), many of the changes were in ProGet's SCA features like Vulnerability Scanning, License Detection, Assessments, SBOMS, and Projects/Builds. Webhooks & Notifiers were also redesigned.

In [ProGet 2023](/docs/proget-upgrade-2023), in addition to the new packaging indexing system, there were several changes surrounding vulnerability scanning. One breaking change was that Docker images will no longer have the `library/` namespace automatically appended on push.

In [ProGet 2022](/docs/proget-upgrade-2022), the hosting platform changed to .NET6 and users initially reported performance issues, but these should be mostly fixed in maintenance releases. In addition, the [Package Consumers](/docs/proget/installation/proget-old-versions-migration) feature was also replaced in favor of [SCA Projects and Releases](/docs/proget/sca/builds).
    
In [ProGet 6.0](/docs/proget-6-0-upgrade-notes), due API Keys changes, some users reported that some [API Keys](/docs/proget/api/apikeys) needed to edited or deleted and recreated to fix permissions issues.

In [ProGet 5.3](/docs/proget-5-3-upgrade-notes), legacy NuGet ("quirks") feeds were removed. You will need to migrate all of these feeds before upgrading.


### Upgrading from ProGet 5.1 or Earlier
:::(Error)
If you're upgrading from ProGet 5.1 or earlier, you should upgrade to ProGet 5.2 first. Upgrading directly is not recommended.
:::

We recommend upgrade to latest ProGet 5.2, then latest ProGet 2026. See: [5.3 notes](/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrading-to-proget-5-2), [ v4 notes](/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrade-notes-for-proget-v4), [v2/v3 notes](/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrading-from-proget-v2-and-v3)

## New Features in ProGet 2026

### Feature Overhaul: Vulnerabilities Management

* Impacted Feed Packages & Builds to Vulnerability Details ([PG-3184](https://issues.inedo.com/issue/PG-3184))

:::(Info)
Talk about the minor risk here
:::




## Other Improvements & Notable Changes

### Health Handler Changes

The `/health` endpoint now provides a simple, plain text error message to allow monitoring systems to convey the system error without parsing JSON. The old health handler is still accessible under `/health/json`.

### Various Changes & Improvements
* **Platform Upgrade (.NET8 to .NET10)**
* **Maven Version Parsing & Endpoint Fixes**; very minor, but added support for parsing 4-segment versions (for vulnerability identification) and artifact versions that don't start with a number
* **"Notifications" Feature**; added via [PG-3233](https://issues.inedo.com/issue/PG-3233) as a preview feature, provides in-application notifications instead of relying on email

* Package Age Compliance Rule ([PG-3162](https://issues.inedo.com/issue/PG-3162))

## Upgrade Process
You should generally perform the upgrade using the same method you used to install.

* On Windows, the most common installation method is using the internet-connected [Inedo Hub](/docs/installation/windows/inedo-hub); see  [HOWTO: Upgrade or Downgrade with the Inedo Hub](/docs/installation/windows/howto-upgrade-downgrade) for more details on how to upgrade.
* On Linux, the most common installation method is using our Docker container; see [Upgrading Docker Containers](/docs/installation/linux/installation-upgrading-docker-containers) for more details.

However, there are other installation options available, including [offline installation](/docs/installation/windows/inedo-hub/offline), [cluster installation](/docs/installation/high-availability-load-balancing/high-availability-load-balancing), and even [manual installation](/docs/installation/windows/manual-installation). If you want to change installation methods, the easiest way is to simply uninstall (by following the process in reverse) and install using the new method.

If you're upgrading from ProGet 5.3 or earlier, it's possible ProGet was installed with the [legacy installer (github.com)](https://github.com/Inedo/inedo-docs/blob/c82fd2881e2f1d0c36e77bc8b8b48e2a2c7b75a9/Content/installation/windows/installation-legacy-traditional-installer.md), The Inedo Hub should be able to upgrade these installations, but you may need to uninstall and then reinstall using the Inedo Hub. 

:::(Warning) (Backup Before Upgrading)
You should make sure your [installation is backed-up](/docs/installation/backing-up-restoring).
:::

While you can upgrade from ProGet 5.0 and later to ProGet 2026 (i.e. there is no need to install intermediate versions), you can only rollback to ProGet 2025 without restoring your database.

## Rolling Back

:::(Error) (Database Restore may be required)
If you want to rollback to ProGet 2024 or earlier, you will need to restore the instance to the previous state. See the [Backing Up & Restoring](/docs/installation/backing-up-restoring) documentation to learn more.
:::

However, if you need to rollback to ProGet 2025, you can do so without restoring the database by simply using the Inedo Hub. While there are database schema changes, they are all backwards-compatible with ProGet 2025, which means you can safely rollback your ProGet installation if there's a showstopper bug, and then upgrade later.
