---
title: "Upgrading to ProGet 2022"
order: 3
url-slug: "proget-upgrade-2022"
---

:::(Error) (ProGet 2022 Upgrade Not Recommended)
If you're currently using ProGet 5.2 ProGet 5.3, or or ProGet 6.0 we do not recommend upgrading to ProGet 2022. 

Instead, **directly upgrade to ProGet 2025** ([see notes](/docs/proget-upgrade-2025)). This article is kept as-is to help show what changed in ProGet 2022.
:::

ProGet 2022 is a major update, and this article provides information about what will change, the impact to your instance, and how to mitigate risk during upgrade.

## Planning for Your Upgrade

ProGet 2022 is a major upgrade, and many of the changes were additive features (available as "preview" features in ProGet 6.0), platform updates (library upgrades, etc.), and UI changes.

There are some key things to keep in mind before upgrading:

1. **Replication has updated/refactored**, and you may need to migrate configuration
2. **Package Consumers has been replaced** with Projects and Releases, and you may want to migrate data
4. Custom-built extensions need to be recompiled
5. **Hybrid user directories were replaced**, and you will need to manually delete old entries
6. **The platform (.NET 4.5.2) was upgraded to .NET 6**, which may mean you need to update hosting configuration and directories. A few customers have also reported performance problems, which we are continuing to investigate.

You can also rollback to ProGet 6.0 if there are issues without restoring your database.

### Upgrading from ProGet 5.3
:::(Info)
If you're upgrading from ProGet 5.3, upgrading to ProGet 2022 should be relatively easy.
:::

There were some API Keys changes in ProGet 6.0, and some users reported that some [API Key](/docs/proget/api/apikeys) needing to edited or deleted/recreated to fix permissions issues. Otherwise, no major features were removed or changed, and the upgrade should be relatively easy.

### Upgrading from ProGet 5.2
:::(Warning)
If you're upgrading from ProGet 5.2, you will need convert all "quirks" feeds before upgrading. Rolling back will also require restoring your database.
:::

While we recommend upgrading *directly* to ProGet 2022 from ProGet 5.2 (and skipping 6.0 and 5.3), there were some substantial changes to the database made in ProGet 5.3. Make sure to read the [Upgrading to ProGet 5.3](/docs/proget-5-3-upgrade-notes) notes to learn what changed.

### Upgrading from ProGet 5.1 or Earlier
:::(Error)
If you're upgrading from ProGet 5.1 or earlier, you should upgrade to ProGet 5.2 first.
:::

We recommend upgrade to latest ProGet 5.2, then latest ProGet 2022. See: [5.3 notes](/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrading-to-proget-5-2), [ v4 notes](/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrade-notes-for-proget-v4), [v2/v3 notes](/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrading-from-proget-v2-and-v3)

## New Features in ProGet 2022

### Updated Feature: Replication Improvements

For ProGet 2022, we developed a new user interface for replication and improved some of the underlying replication code to make it easier to troubleshoot and diagnose. This was developed as a "preview feature" in ProGet 6.0, and you may already have enabled it.

:::(Error) (Replication Data Migration Required)
If you didn't enable the "preview feature" in ProGet 6.0, then replication will not work until you've performed a one-time data migration. This takes seconds, and can be done under the "Replication" tab.
:::

In addition to an improved user experience, the main differences are:
 * Create a single "replication configuration" that uses multiple feeds; in previous versions, feed replication was configured on a per-feed basis
 * Use API Keys that can be changed without editing replication configuration; in previous versions, each feed required its own security token, and API Keys were not used
 * See "incoming" and "outgoing" replications status on a single dashboard; this was very limited in previous versions, and was often confusing to follow
 
See [Feed Replication (6.0 and Earlier)](/docs/proget-6-0-upgrade-notes) to learn more about the differences.

:::(Warning) (Replication Changes Risk Mitigation)
Because the replication changes were implemented side-by-side, you can test the replication changes in ProGet 6.0 by enabling the "preview features" in the latest maintenance version of ProGet 6.0. You can also switch back to using the non-preview behavior.

After you've upgraded to ProGet 2022, you can rollback to ProGet 6.0 if there are issues, and then switch back to the old behavior from within ProGet.
:::


### New Feature: ProGet Enterprise for Edge Computing 

This is a new edition of ProGet that is supported via a new license key in ProGet v2022, and is specialized for [Content Distribution for Edge Computing](/docs/proget/replication-feed-mirroring/proget-howto-replicate-edge-locations).  

Compared to ProGet Enterprise, the main benefit is scaling at 5+ edge nodes (instances):

•	Licensing (pricing) model that makes scaling to hundreds of nodes feasible
•	Dashboard on “hub” instances to give visibility into edge nodes
•	Simplified setup for the edge nodes (no license key required)
•	Working with our team to better improve the product/experience to fit your needs

There is no risk mitigation required, and many of the code changes/additions were introduced as a preview feature throughout ProGet 6.0.


### New Feature: SAML & Security Management Overhaul

We've added some more flexibility for logging-in to ProGet 2022 and made it lot easier to manage security. This was developed as a "preview feature" in ProGet 6.0, and you may already have enabled it:

* Improved UI for managing security and users
* Allow multiple user directories to be searched at once
* Integrate with SAML providers like Azure and PingID
* Import/export Permissions and Built-in Users
* Enable multiple login options at once: Built-in Username/passwords, Windows Authentication, SAML Authentication

These changes involved changing some of the login/authentication code, which may lead to bugs/regressions where users cannot log-in or a connected systems (build/CI server) reports authentication errors. 

In addition, if you're using the **Hybrid User Directory** (which allowed you to query multiple user directories), those user directories will become enabled, and you will see an error on the User Directories page if you had one configured. You can safely delete this directory.

:::(Warning) (Security Changes Risk Mitigation)
Because the security changes were implemented side-by-side, you can test the feature changes in ProGet 6.0 by enabling the "preview features" in the latest maintenance version of ProGet 6.0. You can also switch back to using the non-preview behavior.

In addition, be prepared to run the [locked out troubleshooting](/docs/installation/security-ldap-active-directory) in the event you are locked out after upgrade. You will be able to restore security after that. If that doesn't work, you can rollback to ProGet 6.0.
:::

### New Feature: Software Composition Analysis (SCA)
The "Vulnerabilities" and "Licenses" links in the top navigation have been moved under the new "Reporting & SCA" link. Under this link is also [Projects and Releases](/docs/proget/sca/builds), which will help you identify which of your applications have unwanted licenses or vulnerabilities.

There is no risk mitigation required, as this is a new feature with minimal impact on existing features.

#### Package Consumer Data Migration
This feature is designed to completely replace the existing [Package Consumers (Usage Tracking)](/docs/proget/installation/proget-old-versions-migration) feature, but it's implemented side-by-side. 

:::(Error)
If you've used the [Package Consumers (Usage Tracking)](/docs/proget/installation/proget-old-versions-migration) feature in an earlier version of ProGet, then Software Composition Analysis (SCA) features will be disabled until you migrate the data.
:::

Package Consumers will be completely removed in ProGet 2023.


## Custom Extensions Changes
ProGet  2022 targets `Inedo.SDK-2.0`, and you'll need to rebuild any custom extensions you've created. Note that this version of the SDK targets .NET6.

Previous versions of ProGet targeted different versions of Inedo SDK 1.x.This means that you'll need to update the `Inedo.SDK NuGet` package reference in your project to Inedo-SDK 2.0. See [creating an extension documentation](/docs/inedosdk/extending-inedo-tools-using-the-sdk/inedosdk-extending-creating) to learn more about how to do this.

## Platform Changes (.NET6)
The platform that ProGet uses was upgraded from .NET 4.5.2 to .NET 6.

This shouldn't require you to make any changes, as the Inedo Hub will handle them during installation. You do not need to install the .NET6 Web Hosting Package for IIS, as it's automatically bundled in the ProGet application.

### Web Folder
We have removed the Web folder and the web application is now included in the Service directory. If you have multiple sites in IIS, you will need to update IIS to point to the Service directory now. 

:::(Warning) (.NET6 Requires Multiple Application Pools for Multiple Sites)
Note that each of your sites will require its own application pool, and if you see an error like "ASP.NET Core does not support multiple apps in the same app pool", then you'll want to create a second application pool for your second site.
:::

### 405 Errors & WebDAV
Some users have reported receiving "405 Method Not Allowed" errors after upgrading, and the underlying cause was that the WebDAV IIS module was blocking `PUT` and `DELETE` requests. We're not entirely sure why wasn't blocking those requests earlier.

See [Disabling WebDAV in IIS](https://github.com/Inedo/inedo-docs/blob/44a99e458fe3ac2972ffa7e74b48de8fd342ed18/Content/installation/installing-on-iis/various-iis-disabling-webdav.md) to learn how to fix this.

### Performance Issues
A few customers have reported errors like: *Timeout expired. The timeout period elapsed prior to obtaining a connection from the pool. This may have occurred because all pooled connections were in use and max pool size was reached.*

:::(Info)
**We now recommend setting the `Web.NuGetRequestLimit` advanced configuration value,** which will mimic ASP.NET Framework's old behavior. This setting is available under Admin > Advanced Settings in v2022.10 and later.

Let us know what values work for you; in general we recommend starting at 500, and increasing or decreasing by values of 100 until you find one that causes the issues to go away.
:::
#### Background  & Research on Performance Issues
This seems to impact customers who meet the following criteria: 
 * Very high peak usage, such as hundreds of requests per second
 * Configured with multiple connectors
 * Physical server (Hypervisor) uses newer Xeon-based processors with 8+ cores
 * Virtual server utilizes a fraction of those cores

We believe the issue is related to the fact that .NET Core (our new platform) no longer "automagically" manages the [process model](https://docs.microsoft.com/en-us/dotnet/api/system.web.configuration.processmodelsection) on IIS.  This means that incoming HTTP requests are no longer throttled at the IIS-level, and they will rush in until they reach the next bottleneck - in this case, ADO.NET’s database connection pooling. 

IIS, ASP.NET Framework, and ADO.NET Connection Pooling were designed in a world of  "shared hosting", where a single physical server would host multiple applications. As such, the technologies were generally conservative with utilizing using the server's resources. Of course, you could tweak these if you wanted. This design decision made it simpler to manager by trading-off speed/latency.

ASP.NET Core takes a different approach: it doesn't have built-in resource throttling, and will generally consume whatever resources are available.  This makes it a bit more complex to manage, but it's generally faster.

While you're welcome to tweak ProGet's IIS settings or database connection settings to match your needs, our current guidance and default setting is 1000 connections, which is ten times the current default value of 100.

:::(Info)
Starting in ProGet 2022.5 ([PG-2182](https://issues.inedo.com/youtrack/issue/PG-2182), the default value for connection pools will be `1000` if the default value of `100` is used. If you're using an earlier version, or otherwise modified this value, you can modify your database connection string, which you can edit in the Inedo Hub: 

`«existing-connection-string»;Max Pool Size=1000`.
:::

Our guidance is based on several factors, but most notably that ADO.NET's current default value (100) was decided over twenty years ago in .NET Framework 1.0. This default may have made sense for the time, but with hardware, networks, operating systems, and hypervisors, there's little advantage to limiting these connections.

## Upgrade Process
You should generally perform the upgrade using the same method you used to install.

* On Windows, the most common installation method is using the internet-connected [Inedo Hub](/docs/installation/windows/inedo-hub); see  [HOWTO: Upgrade or Downgrade with the Inedo Hub](/docs/installation/windows/howto-upgrade-downgrade) for more details on how to upgrade.
* On Linux, the most common installation method is using our Docker container; see [Upgrading Docker Containers](/docs/installation/linux/installation-upgrading-docker-containers) for more details.

However, there are other installation options available, including [offline installation](/docs/installation/windows/inedo-hub/offline), [cluster installation](/docs/installation/high-availability-load-balancing/high-availability-load-balancing), and even [manual installation](/docs/installation/windows/manual-installation). If you want to change installation methods, the easiest way is to simply uninstall (by following the process in reverse) and install using the new method.

If you're upgrading from ProGet 5.3 or earlier, it's possible ProGet was installed with the [legacy installer (github.com)](https://github.com/Inedo/inedo-docs/blob/c82fd2881e2f1d0c36e77bc8b8b48e2a2c7b75a9/Content/installation/windows/installation-legacy-traditional-installer.md), The Inedo Hub should be able to upgrade these installations, but you may need to uninstall and then reinstall using the Inedo Hub. 

:::(Warning) (Backup Before Upgrading)
You should make sure your [installation is backed-up](/docs/installation/backing-up-restoring).
:::

While you can upgrade from ProGet 5.0 and later to ProGet 2022 (i.e. there is no need to install intermediate versions), you can only rollback to ProGet 6.0 without restoring your database.


## Rolling Back

:::(Error) (Database Restore may be required)
If you want to rollback to ProGet 5.3 or earlier, you will need to restore the instance to the previous state. See the [Backing Up & Restoring](/docs/installation/backing-up-restoring) documentation to learn more.
:::

However, if you need to rollback to ProGet 6.0, you can do so without restoring the database by simply using the Inedo Hub. While there are database schema changes, they are all backwards-compatible with ProGet 6.0, which means you can safely rollback your ProGet installation if there's a showstopper bug, and then upgrade later.