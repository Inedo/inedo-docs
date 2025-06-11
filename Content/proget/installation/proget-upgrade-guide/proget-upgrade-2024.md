---
title: "Upgrading to ProGet 2024"
order: 2
url-slug: "proget-upgrade-2024"
---

ProGet 2024 is a major update, and this article provides information about what will change, the impact to your instance, and how to mitigate risk during upgrade.

:::(Error) (ProGet 2024 Upgrade Not Recommended)
If you're currently using ProGet 5.2 ProGet 5.3, ProGet 6.0, ProGet 2022, or ProGet 2023 we do not recommend upgrading to ProGet 2024.

Instead, **directly upgrade to ProGet 2025** ([see notes](/docs/proget-upgrade-2025)). This article is kept as-is to help show what changed in ProGet 2023.
:::

## Planning for Your Upgrade

ProGet 2024 is a major upgrade, and many of the changes were additive features (available as "preview" features in ProGet 2023), platform updates (library upgrades, etc.), and UI changes.

### Upgrading from ProGet 2023
:::(Info)
If you're upgrading from ProGet 2023, upgrading to ProGet 2024 should be relatively easy.
:::

If there are issues, you can rollback to ProGet 2023 without restoring your database.

### Upgrading from Older Versions

:::(Error) (Required Data Migration & Disabled Retention Rules)
After upgrading, ProGet will automatically initiate a data migration; see [New Package Indexing System in ProGet 2023](/docs/proget-upgrade-2023#new-package-indexing-system-in-proget-2023) to learn about this process. 

The migration should be relatively quick and require no user intervention. 

After migration is complete, you will need to re-enable retention rules on your feeds. 
:::

If you're currently using ProGet 5.2, ProGet 5.3, ProGet 6.0, or ProGet 2022 we recommend directly upgrading to ProGet 2024. While it won't *hurt* to do incremental upgrades, there's almost never a benefit.

Please read the upgrade notes from each version to learn what changed and how to mitigate risks. To summarize:
    

In  [ProGet 2023](/docs/proget-upgrade-2023), in addition to the new packaging indexing system, there were several changes surrounding vulnerability scanning. One breaking change was that Docker images will no longer have the `library/` namespace automatically appended on push.

In [ProGet 2022](/docs/proget-upgrade-2022), the hosting platform changed to .NET6 and users initially reported performance issues, but these should be mostly fixed in maintenance releases. In addition, the [Package Consumers](/docs/proget/installation/proget-old-versions-migration) feature was also replaced in favor of [SCA Projects and Releases](/docs/proget/sca/builds).
    
In [ProGet 6.0](/docs/proget-6-0-upgrade-notes), due API Keys changes, some users reported that some [API Keys](/docs/proget/api/apikeys) needed to edited or deleted and recreated to fix permissions issues.

In [ProGet 5.3](/docs/proget-5-3-upgrade-notes), legacy NuGet ("quirks") feeds were removed. You will need to migrate all of these feeds before upgrading.


### Upgrading from ProGet 5.1 or Earlier
:::(Error)
If you're upgrading from ProGet 5.1 or earlier, you should upgrade to ProGet 5.2 first. Upgrading directly is not recommended.
:::

We recommend upgrade to latest ProGet 5.2, then latest ProGet 2024. See: [5.3 notes](/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrading-to-proget-5-2), [ v4 notes](/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrade-notes-for-proget-v4), [v2/v3 notes](/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrading-from-proget-v2-and-v3)

## New Features in ProGet 2024

### New Feature: Policies & Compliance Rules

ProGet has allowed administrators to prevent downloads of unwanted packages. 

In earlier versions of ProGet, this was primarily done by defining feed- and global-level license rules and assessing vulnerabilities that are configured to block downloads.

In ProGet 2024, download blocking is accomplished using the new [Policies & Compliance Rules](/docs/proget/sca/policies) feature, which allows you to define a rules that determine whether packages are compliant, noncompliant, or neither (i.e. "warn"). Each feed can then determine whether noncompliant packages are blocked.

When upgrading to ProGet 2024, your existing licensing and vulnerability configuration will be converted into policies that you can edit by navigating to Manage Feed > Policies & Blocking. Although the User Interface will be different, it should behave largely the same.

:::(Info)
Policies were introduced as a preview feature starting in ProGet 2023.xx. Enabling the preview feature will help mitigate risk of the new feature behaving differently, and you'll have the ability to disable the feature in ProGet 2023.
:::

ProGet 2024 will retain the old database tables for roll back purposes, but you will not be able to enable the old feature.


### Redesigned Feature: Vulnerability Scanning & Assessments

Following the success of ProGet 2023's "offline" vulnerability database and the launch of [Inedo Security Labs](https://security.inedo.com/), we redesigned ProGet's vulnerability scanning and assessment feature from the ground-up.  

It works largely in the same way: ProGet will detect vulnerabilities on packages and you can assess those vulnerabilities manually or automatically to determine if a package is downloadable.

There are a few differences when compared with ProGet 2023's built-in database (i.e. PGVC):
* Assessments can be scoped to Feeds or Projects
* Expiry dates are tied to Assessments, and Assessment Types provide only the default
* Assessment Types no longer determine if a package is blocked; this is instead handled by Policy & Complaince rules

The biggest changes is under-the-hood, in the code and database model that the feature uses. In addition to a new set of database tables to store vulnerabilities and assessments, ProGet no longer uses the "vulnerability source" component.

This means that your existing data will need to be migrated, and ProGet 2024 will automatically handle that during the upgrade process. This will only copy data - your existing data will be kept for downgrade purposes.

:::(Info)
These vulnerability changes were introduced side-by-side in ProGet 2023 and can be enabled as a preview feature starting in ProGet 2023.29. Enabling the preview feature will help mitigate risk of the new feature behaving differently, and you'll have the ability to disable the feature in ProGet 2023.
:::
ProGet 2024 will retain the old database tables for roll back purposes, but you will not be able to enable the old feature.

### Redesigned Feature: SCA Projects
[Projects for Software Composition Analysis (SCA)](/docs/proget/sca/builds) were introduced in ProGet 2023. Based on user feedback and research, we redesigned this feature in ProGet 2024.

Conceptually it's very similar, and will integrates into CI/CD pipeline in exactly the same way. There are a few key changes:  
* "Releases" are now called "Builds"
* Builds are "promoted" through "Stages" like Integration, Testing, etc.
* Actions occur at different stages, such as analyzing packages and archiving other builds

When upgrading to ProGet 2024, your existing releases and will be migrated into the redesigned feature.

:::(Info)
The Projects & Builds features were introduced as a preview feature starting in ProGet 2023.30. Enabling the preview feature will help mitigate risk of the new feature behaving differently, and you'll have the ability to disable the feature in ProGet 2023.
:::

ProGet 2024 will retain the old database tables for roll back purposes, but you will not be able to enable the old feature.

### Redesigned Feature: Webhooks & Notifiers

Although UI and functionality is effectively the same, the [Webhooks](/docs/proget/administration/proget-notifications-webhooks) feature was reimplemented to addresses many performance issues we've seen over the years. In addition, individual Webhook results be logged, making it much easier to troubleshoot. You'll be able to create Webhooks for new events, like New Vulnerability Detected.

The new feature, called [Webhooks & Notifiers](/docs/proget/administration/proget-notifications-webhooks), also supports sending emails. 

:::(Info)
The Webhooks & Notifiers features were introduced as a preview feature starting in ProGet 2023.29. Enabling the preview feature will help mitigate risk of the new feature behaving differently, and you'll have the ability to disable the feature in ProGet 2023.
:::

 ProGet 2024 will retain the old database tables for roll back purposes, but you will not be able to enable the old feature.

### New Command-line Tool pgutil

`pgutil` is a new, cross-platform command line tool that's easy to download and install. See [Getting Started with pgutil](/docs/proget/api/pgutil) to learn more.

This will replace `pgscan` and will go beyond SCA to include things like auditing packages, builds, uploading packages, creating feeds, etc. `pgscan` will still work but will no longer be maintained. We recommend upgrading to `pgutil`.


### Features & Licensing Changes

To help keep ProGet competitive and in line with other tools in the market, we plan make ProGet Enterprise be the full-featured edition appropriate for larger teams and organizations. ProGet Free and ProGet Basic be the feature-limited editions suitable or evaluation and smaller teams.
    
We don't want to "take away" features from existing ProGet Basic users in ProGet 2024, so instead we'll limit some of the new features by functionality and quantity. These are documented in detail on the [License Editions & Restrictions](/docs/proget/administration/license) page, but they are subject to change - and if you feel we got them wrong, please let us know.

ProGet Basic edition will have the following limitations:
* **Policies** - only one shared policy may be used, rule exceptions are limited to three per policy, and policies may not be scoped to feeds/projects
* **SCA API Rate Limit** - SCA actions like `pgutil audit` are limited to ten per hour
* **Alerts/Notifications** are limited to Custom Webhooks, not using Email or Slack/Team templates 
* **Projects & Builds** cannot be associated with policies and you are limited to 1000 active builds
* **API Security Options** are limited

ProGet Free edition now has a rate-limited Delete API (10 deletes per hour).

## Other Improvements & Notable Changes

### Style / Font Tweaks

We made some minor style changes; most notably the default font changed from [Segoe UI](https://learn.microsoft.com/en-us/typography/font-list/segoe-ui) (if available on the system) to [Karla](https://fonts.google.com/specimen/Karla) (always embedded in ProGet).


### Docker Layer Scanning
Introduced as a preview feature in ProGet v2023.26, container images can be scanned for information such as operating system and used packages. When enabled, images are scanned on upload if they are small, or queued to be scanned in the background if they are large. Packages used by an image will be displayed in the Packages tab when viewing a Docker tag.

### Database View/Query Refactoring 
When ProGet 2023 was first released, we had a handful of customers with high traffic (100+ requests/sec) report database timeouts and/or deadlock errors as a result of the new indexing system. These were related to issues with the execution plans chosen by SQL Server's query analyzer and were largely  specific to the version of SQL Server Engine as well as the dataset (especially statistics).

We've carefully refactored the queries/views used in the indexing system to avoid notoriously buggy SQL Server features like [MERGE Statements](https://www.mssqltips.com/sqlservertip/3074/use-caution-with-sql-servers-merge-statement/) and Indexed views. This should help with performance, but there's always room for more, so if you're encountering these kinds of errors or drastic performance differences, let us know.

:::(Internal) (Never Done :()
### Connector Changes/Refactoring
Although packages were "centralized" in ProGet 2023's new indexing system, a lot of the "middleware" code - including connectors - was not. Instead, it was simply "rewired" to the new database tables. This lead to a lot of trivial but predictable bugs, like deprecation working differently on one feed versus another. 

In ProGet 2024, we've reorganized/refactored the feed/connector middleware code to be based on this centralized indexing system. In addition to more reliable code, this allowed us t:
* add "connector" packages to the Common Package API 
* perform operations like "what is the latest version of a package" during SCA/project analysis
* expose internal indexes used for remote repositories on certain feed types
* ProGet to ProGet feeds are much more efficient, relying on a specialized API
:::

### Python (PyPI) Changes/Refactoring
As part of the connector changes, the Python (PyPI) feed's middleware was rewritten to account for changes to the PyPI API over the years, including the deprecation of the Search API at PyPI.org.


### SDK Changes
ProGet 2024 targets `Inedo.SDK-3.0` and .NET8, and you'll need to rebuild any custom extensions you've created. Note that several items were removed from the SDK; see [Discontinued Features in ProGet 2024](#discontinued-features-in-proget-2024)

### API Key Changes
We added several additive features to API Keys, including
* Set Expiry Date so that keys can be valid for only a certain amount of time
* Added cryptographic hash storage option so that keys can only be seen once 
* Allow administrators to block `user:password` API keys

### Various Changes & Improvements
* **Platform Upgrade (.NET6 to .NET8)**
* **Health API** will return a status code of `500` if there are any issues at all (including service not running)
* **Announcement Banner** allows you to communicate system-level messages to end users using the top-banner in ProGet's Web UI
* **Rate Limit for Delete API** for ProGet Free edition
* **Project Badge Feature** lets you show the compliance status of projects/builds
* **NuGet README** is now displayed on the package overview page
* **All Packages can be Deprecated/Unlisted**, even if the API doesn't support or use that information

## Discontinued Features in ProGet 2024
### "Manual" Vulnerabilities
Earlier versions of ProGet allowed you to add a "manual" vulnerability record on a specific package version (or version ranges), or on a container image layer. These were used almost exclusively as a "hack" to block downloads of certain packages, are have since been discontinued in ProGet 2024 favor of other tools to prevent usage of noncompliant packages.

### Extensions: OSS Index, Clair, WhiteSource
Earlier versions of ProGet had limited vulnerability detection and compliance functionality, which is why we integrated with third-party tools like OSS Index, Clair, and WhiteSource. ProGet 2024's capabilities have significantly advanced, and are a far superior solution.

If you're currently using these integrations, see the following guides on how to migrate before upgrading:
* [Discontinued: WhiteSource](/docs/proget/installation/proget-old-versions-migration/proget-compliance-whitesource)
* [Discontinued: Clair](/docs/proget/installation/proget-old-versions-migration/proget-compliance-clair)
* [Discontinued: OSS Index](/docs/proget/installation/proget-old-versions-migration/proget-compliance-ossindex)

### SDK: Vunlerability Sources & Package Access Rules
The `PackageAccessRule` and `VulnerabilitySource` were designed for and used by the OSS Index, Clair, and WhiteSource extensions. 


## Upgrade Process
You should generally perform the upgrade using the same method you used to install.

* On Windows, the most common installation method is using the internet-connected [Inedo Hub](/docs/installation/windows/inedo-hub); see  [HOWTO: Upgrade or Downgrade with the Inedo Hub](/docs/installation/windows/howto-upgrade-downgrade) for more details on how to upgrade.
* On Linux, the most common installation method is using our Docker container; see [Upgrading Docker Containers](/docs/installation/linux/installation-upgrading-docker-containers) for more details.

However, there are other installation options available, including [offline installation](/docs/installation/windows/inedo-hub/offline), [cluster installation](/docs/installation/high-availability-load-balancing/high-availability-load-balancing), and even [manual installation](/docs/installation/windows/manual-installation). If you want to change installation methods, the easiest way is to simply uninstall (by following the process in reverse) and install using the new method.

If you're upgrading from ProGet 5.3 or earlier, it's possible ProGet was installed with the [legacy installer (github.com)](https://github.com/Inedo/inedo-docs/blob/c82fd2881e2f1d0c36e77bc8b8b48e2a2c7b75a9/Content/installation/windows/installation-legacy-traditional-installer.md), The Inedo Hub should be able to upgrade these installations, but you may need to uninstall and then reinstall using the Inedo Hub. 

:::(Warning) (Backup Before Upgrading)
You should make sure your [installation is backed-up](/docs/installation/backing-up-restoring).
:::

While you can upgrade from ProGet 5.0 and later to ProGet 2024 (i.e. there is no need to install intermediate versions), you can only rollback to ProGet 2023 without restoring your database.

## Rolling Back

:::(Error) (Database Restore may be required)
If you want to rollback to ProGet 2022 or earlier, you will need to restore the instance to the previous state. See the [Backing Up & Restoring](/docs/installation/backing-up-restoring) documentation to learn more.
:::

However, if you need to rollback to ProGet 2023, you can do so without restoring the database by simply using the Inedo Hub. While there are database schema changes, they are all backwards-compatible with ProGet 2023, which means you can safely rollback your ProGet installation if there's a showstopper bug, and then upgrade later.