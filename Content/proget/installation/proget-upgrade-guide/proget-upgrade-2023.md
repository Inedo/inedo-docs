---
title: "Upgrading to ProGet 2023"
order: 3
url-slug: "proget-upgrade-2023"
---

:::(Error) (ProGet 2023 Upgrade Not Recommended)
If you're currently using ProGet 5.2 ProGet 5.3, ProGet 6.0, or ProGet 2022 we do not recommend upgrading to ProGet 2023.

Instead, **directly upgrade to ProGet 2025** ([see notes](/docs/proget-upgrade-2025)). This article is kept as-is to help show what changed in ProGet 2023.
:::

ProGet 2023 is a major update, and this article provides information about what will change, the impact to your instance, and how to mitigate risk during upgrade.

## Planning for Your Upgrade

ProGet 2023 is a major upgrade, and many of the changes were additive features (available as "preview" features in ProGet 2022), platform updates (library upgrades, etc.), and UI changes.

### Upgrading from ProGet 2022

:::(Error) (Required Data Migration & Disabled Retention Rules)
The [New Package Indexing System in ProGet 2023](#new-package-indexing-system-in-proget-2023) requires that your existing data is migrated. This will happen automatically and should be relatively quick, requiring no user intervention. 

Afterward migration is complete, you will need to re-enable retention rules on your feeds. 
:::

If there are issues, you can rollback to ProGet 2022 without restoring your database.

### Upgrading from Older Versions

Please read the upgrade notes from each version to learn what changed and how to mitigate risks. To summarize:

In [ProGet 2022](/docs/proget-upgrade-2022), the hosting platform changed to .NET6 and users initially reported performance issues, but these should be mostly fixed in maintenance releases. In addition, the [Package Consumers](/docs/proget/installation/proget-old-versions-migration) feature was also replaced in favor of [SCA Projects and Releases](/docs/proget/sca/builds).
    
In [ProGet 6.0](/docs/proget-6-0-upgrade-notes), due API Keys changes, some users reported that some [API Keys](/docs/proget/api/apikeys) needed to edited or deleted and recreated to fix permissions issues.

In [ProGet 5.3](/docs/proget-5-3-upgrade-notes), legacy NuGet ("quirks") feeds were removed. You will need to migrate all of these feeds before upgrading.


### Upgrading from ProGet 5.1 or Earlier
:::(Error)
If you're upgrading from ProGet 5.1 or earlier, you should upgrade to ProGet 5.2 first. Upgrading directly is not recommended.
:::

We recommend upgrade to latest ProGet 5.2, then latest ProGet 2023. See: [5.3 notes](/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrading-to-proget-5-2), [ v4 notes](/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrade-notes-for-proget-v4), [v2/v3 notes](/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrading-from-proget-v2-and-v3)

## New Package Indexing System in ProGet 2023

In ProGet 2022 and earlier, package metadata was indexed in feed-specific database tables like `NpmPackages` and `NuGetPackageVersions`. This made cross-feed queries (such as showing recently published packages) complex and relatively slow and adding new features difficult.

In ProGet 2023, we centralized this metadata into new tables like `Packages` and `FeedPackages`. This enabled a number of features in ProGet 2023, including the new Packages API and the ability to Deprecate Packages, and will make it much easier to add new feed types and related features down the road.

However, this is effectively a new database architecture, so you should prepare for regressions and rollbacks just in case.

:::(Info) (Reported Performance Issues)
A handful of customers with high traffic (100+ requests/sec) to the NuGet v2 API have reported database timeouts and/or deadlock errors. Many of these have been related to issues with the execution plans chosen by SQL Server's query analyzer, and they are specific to the version of SQL Server Engine as well as the dataset (especially statistics).

By working closely with these customers, we've been able to identify and implement several paths for optimization (query hints, etc.). There's always room for more, so if you're encountering these kinds of errors or drastic performance differences, let us know.
:::

#### Data Migration Required (Automatic)
After upgrading, when ProGet 2023 starts for the first time, ProGet will disable your feeds and copy package data from the feed-specific tables to the central tables. This should be relatively fast, but expect several minutes of downtime if you have 100k+ packages.

You'll be able to monitor the status on the ProGet home page (`/` in your ProGet instance), and you'll also be notified of any errors during this migration. While we've tested this extensively with multiple customer datasets, it's possible that invalid metadata or other bugs from old packages will cause a migration failure.

#### What to do if the data migration fails?
On the ProGet home page, you'll be able to retry the migration with different options such as: only migrating specific feeds, ignoring warnings, or verbose logging.

If the error seems like it's related to database connectivity (e.g. a deadlock or timeout error), we recommend to try again with the default options. If the error seems to be a corrupt package, you can try ignoring warnings. In any either case, you can view the detailed results of migrations under Admin > Executions.

**If there are any errors please [let us know](https://inedo.com/support)** by submitting a ticket or forum post. Even if it's an intermittent issue (like database connectivity), we'd like to make sure we document how other customers have resolved it.

#### Rolling Back after a Failed Migration
You can rollback to ProGet 2022 by using the "Downgrade" feature in the Inedo Hub. This will not require a database restore. When you upgrade to ProGet 2023 again, a new data migration will initiated.

However, because  ProGet 2023 does not write to the old tables, any new packages added will not appear in ProGet 2022. We recommend rolling back as soon as possible, as to not lose new data.

#### Enable Retention Rules
Out of an abundance of caution, retention rules will *not* be enabled on the new indexing system. You will need to enable package retention on a feed-by-feed basis after upgrading, and you'll have an opportunity to test (dry-run) the rules before enabling.

## New Features in ProGet 2023
### Redesigned Feature: Vulnerability Scanning

ProGet 2023 includes an "offline" vulnerability database called ProGet Vulnerability Central (PGVC) that is periodically updated on a nightly basis, and whenever you upgrade ProGet. Unlike third-party vulnerability services (such as OSS Index) that require using an API or an overnight download, PGVC vulnerability data will be available immediately, even on remote packages.

Having this built-in database led us to rethink the vulnerabilities feature, and make the following changes:
* You now can enable or disable vulnerabilities on a feed (instead of relying on the confusing Feed Usage setting)
* On remote packages (i.e. not cached or local)...
   * vulnerabilities from PGVC will always be displayed
   * you can assess these vulnerabilities before downloading or using the package
   * if "block unassessed" is configured, attempting to download a remote package will result in an error
   * otherwise, the vulnerabilities will be added to ProGet and considered unassessed
 * vulnerability sources like OSS Index are now called "third-party vulnerability sources"

On cached or local packages, vulnerabilities will behave largely the same: if a new vulnerability is downloaded in a PGVC update, the package will get an unassessed vulnerability

The experimental "on-demand vulnerability scanning" feature was also removed, as it rarely worked due to rate limitations of OSS Index.

:::(Warning) (PGVC may be Disabled by Default & Duplicate Vulnerabilities)
If you're currently using a third-party vulnerability source like OSS Index, then PGVC will not be enabled. If you enable the PGVC, you will very likely receive duplicate vulnerability reports and see a lot of unassessed vulnerabilities. Unfortunately, these cannot be automatically resolved. 
:::

There is no risk mitigation required, as this is additive and will not impact your existing data or configuration. In addition, this was introduced as a preview feature in ProGet 2022.20.

### Improved Feature: NuGet Symbol Serving

NuGet feeds in ProGet 2023 support symbol package (i.e. a `.snupkg` file) to be stored alongside the main  package (i.e. the `.nupkg` file). 

When this feature is enabled, you can use the `dotnet nuget push --symbol` command to push both packages to the same feed. If you've already enabled symbol serving on your NuGet feed and would like to try this combine approach, you can change the symbol serving setting from `Legacy (.symbols.nupkg)`  to `Mixed (both formats)`. See the updated [Source and Symbol Server](/docs/proget/feeds/nuget/symbol-and-source-server) documentation to learn more.

There is no risk mitigation required, as trying to run `dotnet nuget push --symbol` would simply result in an error.  In addition, this was introduced as a preview feature in  ProGet 2022.20.

### Improved Feature: Active Directory Integration

ProGet 2023 supports the latest version (v4) of [our Active Directory / LDAP integration](/docs/installation/security-ldap-active-directory). This version is a rewrite of the previous version (v3), and has added optimizations, advanced LDAP filtering, and is tied to a single domain, which simplifies LDAP queries. It also includes a new Microsoft Active Directory Group Search Type for improved performance and customization of LDAP queries, supporting large forests and other LDAP providers. See [Active Directory / LDAP (v4)](/docs/installation/security-ldap-active-directory/various-ldap-v4-advanced) to learn more.

There is no risk mitigation required, as this is a separate user directory and will not be enabled by default. Support was introduced as a preview feature in ProGet 2022.20.
    
### Improved UX: "Feed Usage" -> "Feed Features"

In ProGet 2022 and earlier, you could set the "Feed usage" on a feed to be Free/Open Source, Private/Internal, Validated/promoted, or Mixed. This setting controlled which tabs and messages were  displayed in the user interface and whether packages without licenses would be blocked. For example, "Private/Internal packages" wouldn't  display the license or vulnerability information, nor block unlicensed packages.

In ProGet 2023, this setting was changed to "Feed Features" to give more granular control over what to show on the User Interface. You can now control whether to:
* Display vulnerability information and enforce download blocking rules
* Display license information and enforce download blocking rules

There is no risk mitigation required. This was introduced as a preview feature in ProGet 2022.21, and it was enabled by default on new installations.

### New Feature: Package Status (Deprecating, etc)
In earlier versions of ProGet, some feeds allowed you to set a "Listed" status, which would hide the package in some views. We've expanded this package status into a number of other fields, which you can now change on each package:

* **Downloadable**:  allows you to override the licensing/vulnerability rules
* **Unlisted**: reduce discovery by hiding in some search results
* **Deprecated**: inform package consumers if they have it installed/used in their projects
* Other information like Download count, publish date, etc.

## Other Improvements & Notable Changes

### Various Changes & Improvements
* **Universal Packages can now be created and edited using the Web UI**; this was introduced as a preview feature in ProGet 2022.21. If there's interest in other feed/package types, let us know.
* **Improved UX for Retention Rules**; you can test (dry run) an individual retention rule, enable/disable rules at the feed level, and there's a new editor interface 
* **HTTP/HTTPS settings can be configured from within ProGet**, on the Administration page, if you're using the Integrated Web Server on Windows; see [Configuring HTTPS on the Integrated Web Server](/docs/installation/windows/web/installation-windows-https-support#configuring-https-on-the-integrated-web-server) to learn more
* **Improved UX for Cluster Management**, configuring ProGet as a server cluster is much simpler, especially the "service messenger"
* Webhooks can be disabled
* Asset items (files, folders) can be renamed
* Conda feeds now support `constrains`
* Package files can be deleted from the UI
* Docker feeds can be configured to block pushing external manifests
* Deletion records can be maintained/exported (used by replication)
* Edge nodes now support ProGet connectors
* SPDIX licenses with `OR` are automatically parsed
* Package URL is now an as "externalReference" on generated  SBOM
* "Replication Status" is now on the  Health API

### Docker Implicit "library/" Namespace
:::(Info)
Starting in ProGet 2023, the `library/` prefix will no longer be automatically prepended to image repository names without namespaces. This may impact your automation scripts that rely on this behavior.
:::
In ProGet 2022 and earlier, if you pushed an image without a namespace (e.g. `myapp`) or pulled an image from a connector without a namespace (e.g. `debian` at dockerhub), then ProGet would automatically prepend a `library/` prefix to the repository name.

For example, if you pushed `myapp` to ProGet, then `library/myapp` would be stored. Likewise, `debian` and `library/debian` This behavior was modeled off of earlier versions of the Docker specification.


## Upgrade Process
You should generally perform the upgrade using the same method you used to install.

* On Windows, the most common installation method is using the internet-connected [Inedo Hub](/docs/installation/windows/desktophub-overview); see  [HOWTO: Upgrade or Downgrade with the Inedo Hub](/docs/installation/windows/inedo-hub-upgrade-downgrade) for more details on how to upgrade.
* On Linux, the most common installation method is using our Docker container; see [Upgrading Docker Containers](/docs/installation/linux/installation-upgrading-docker-containers) for more details.

However, there are other installation options available, including [offline installation](/docs/installation/windows/desktophub-overview/desktophub-offline), [cluster installation](/docs/installation/high-availability-load-balancing/high-availability-load-balancing), and even [manual installation](/docs/installation/manual-installation). If you want to change installation methods, the easiest way is to simply uninstall (by following the process in reverse) and install using the new method.

If you're upgrading from ProGet 5.3 or earlier, it's possible ProGet was installed with the [legacy installer](/docs/installation/windows/installation-legacy-traditional-installer), The Inedo Hub should be able to upgrade these installations, but you may need to uninstall and then reinstall using the Inedo Hub. 

:::(Warning) (Backup Before Upgrading)
You should make sure your [installation is backed-up](/docs/installation/backing-up-restoring).
:::

While you can upgrade from ProGet 5.0 and later to ProGet 2023 (i.e. there is no need to install intermediate versions), you can only rollback to ProGet 2022 without restoring your database.

## Rolling Back

:::(Error) (Database Restore may be required)
If you want to rollback to ProGet 6.0 or earlier, you will need to restore the instance to the previous state. See the [Backing Up & Restoring](/docs/installation/backing-up-restoring) documentation to learn more.
:::

However, if you need to rollback to ProGet 2022, you can do so without restoring the database by simply using the Inedo Hub. While there are database schema changes, they are all backwards-compatible with ProGet 2022, which means you can safely rollback your ProGet installation if there's a showstopper bug, and then upgrade later.