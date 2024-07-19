---
title: "Upgrading to ProGet 5.3 (2020)"
order: 5
url-slug: "proget-5-3-upgrade-notes"
---

:::(Error) (ProGet 5.3 Upgrade Not Recommended)
If you're currently using ProGet 5.2 we do not recommend upgrading to ProGet 5.3. 

Instead, **directly upgrade to ProGet 2024** ([see notes](/docs/proget-upgrade-2024)). This article is kept as-is to help show what changed in ProGet 5.3.
:::

ProGet 5.3 is a major upgrade, with a number of breaking changes and significant new features. Review this article carefully before deciding when and how to upgrade.

:::(Warning)
**Note**: ProGet 5.3 has some substantial changes to the database. Please be sure to back up your database prior to upgrading. You will not be able to rollback without a database restore.
:::
### New Features & Breaking Changes
- PostgreSQL Support on ProGet for Linux Removed
- Legacy NuGet quirks feed feature removal
- NuGet feeds now use JSON-LD (v3) API
- Enhanced container versioning feature 
- Container vulnerability scanning feature 
- Container layer visibility feature
- Helm chart container visibility feature 
- New Feature: Container & Package Usage 
- New Container, Packages, and Feed Management UI 

### Minor/Behavioral Changes

- Universal Packages: Added the capability to add a short description in the metadata
- Universal Packages: Added support for build metadata
- NuGet: Changed the response code from 401 to 409 when a NuGet package cannot be overwritten
- NuGet: Display "Id" in NuGet Results instead of "Title"
- Webhooks: Added support for ':' in addition to '=' in webhook headers
- ProGet Feeds: Added the ability to leverage x-forwarded-* headers when proxying requests to ProGet
- Renamed Legacy "Deployment" Feeds to "NuGet" Feeds
- For API/Database, renamed "ProGet" Feeds to "Universal" Feeds
- Removed manual vulnerability sources and added support for direct vulnerabilities

## How Do I Upgrade?

:::(Warning) (ProGet v5.2 and lower users)
You will need to first [Upgrade to ProGet 5.2](/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrading-to-proget-5-2) prior to upgrading to ProGet 5.3. Make sure to resolve any [breaking changes](#new-features-breaking-change-details) prior to upgrading.
:::

On Windows, ProGet may be upgraded by running the latest Inedo Hub installer, so make sure the database gets backed up. Note that during upgrade, some schema changes will be applied.

Linux (i.e., Docker) installations may be upgraded by pulling the latest `inedo/proget` image and restarting the container, as per the [Linux Installation Guide](/docs/installation/linux/docker-guide).

#### Rollback
See [restoring your ProGet instance](/docs/installation/linux/docker-guide) for how to rollback.

## New Features & Breaking Change Details

#### Breaking Change: PostgreSQL Support Removed
As of ProGet 5.3, PostgreSQL is no longer supported as a database engine. ProGet will only support SQL Server (all editions including Express) on Windows or [Linux](https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-overview?view=sql-server-ver15) going forward.

**Impact:** Any installation using PostgreSQL will need to be [migrated to SQL Server](https://inedo.com/support/kb/1168/proget-feed-migration) prior to the upgrade.

#### Breaking Change: NuGet (Quirks) Feed Removed
NuGet "Quirks" feeds have been _deprecated_ beginning with ProGet 5.0. In ProGet 5.3, Quirks feeds have been removed. You will need to [migrate your NuGet Quirks feeds](/docs/proget/feeds/nuget#legacy) prior upgrading to 5.3.

**Impact:** Any NuGet Quirks feeds that have not been migrated, will cause the upgrade to fail.

#### New Feature: NuGet feeds now use JSON-LD (v3) API
Both NuGet.org and the NuGet client tools support two different API protocols -- ODATA (v2) and JSON-LD (v3) -- and now ProGet does as well. When JSON-LD (v3) is enabled on a NuGet feed, the v3 API URL will be shown on the NuGet feed page.

Impact: newly-created feeds will default to supporting both API endpoints, and existing feeds may be configured to do so. Connectors to `nuget.org` will always use the JSON-LD (v3) endpoint of `https://api.nuget.org/v3/index.json`, regardless of what you configured. You can add an override token (`#v2`) at the end of the nuget url, such as "http://nuget.org/api/v2#v2", to force the ODATA (v2) protocol

#### New Feature: Enhanced Container Versioning

Container registries can now, optionally, restrict how you can use tags in your container registries. You can enable _strict (SemVer2)_ versioning on a registry-by-registry basis, which will restrict how release (SemVer-formatted), virtual (stable one- or two-part formatted), and informational (the _latest_ tag) tags are applied via the ProGet API.

**Impact:** This will not affect existing tags or replication, only tags applied via the ProGet API.

#### New Feature: Container Vulnerability Scanning

We have added our [vulnerability assessment process](/docs/proget/sca/vulnerabilities) for both manual and automated vulnerability scanning to Container registries. ProGet also now supports automatic container vulnerability scanning using [RedHat's Clair API](https://github.com/quay/clair) to detect vulnerabilities at the container layer level.

**Impact:** This follows our standard assessment process for packages, and unless enabled in advanced settings, containers will only be blocked when a vulnerability is in the _blocked_ status.

#### New Feature: Container Layer Visibility
Container layers have always been visible in ProGet, but until 5.3, they have not included much detail. Starting in 5.3, we now give more details around the container's layers, including what commands went into creating that layer and if vulnerabilities have been found in that layer.

**Impact:** Existing container images will also be converted to display this new information.

#### New Feature: Helm Chart Container Visibility

Helm charts are a way of defining a Kubernetes environment, from its architecture to its container images. ProGet will now display which containers are used by a chart and automatically link them to containers in your local registries.

**Impact:** This will only work for Helm charts that their values.yaml uses the Helm best practices for [defining containers](/docs/proget/feeds/helm).

**Upgrade Impact:** In order for existing charts to be parsed, you will need to set the _Web.BaseUrl_ in _Administration > Advanced Settings_.

#### New Feature: Container & Package Usage
ProGet can now connect to Otter and Kubernetes to show where different packages and container images are being used within your environment.

#### New Container, Packages, and Feed Management UI
We have redesigned and rewritten the feed, repository, and package user interfaces to more easily display the complex information and expose features.

For a short while, the old View Package and Manage Feed pages will be available, and are accessible by clicking on the notice under the navigation header.