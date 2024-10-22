---
title: "Legacy NuGet Feeds and API in ProGet"
order: 7
---

As part of ProGet's package management system, support for older technologies like the NuGet ODATA (v2) API and Legacy NuGet (Quirks) feeds remains for certain use cases. 

In this article, we’ll cover:
* The NuGet ODATA (v2) API for custom queries
* Management and migration of Legacy (Quirks) NuGet feeds.

## NuGet ODATA (v2) API

NuGet feeds can also be queried with an older API, called the ODATA (v2) API.

This API is not very well documented, and requires understanding the [ODATA protocol](http://www.odata.org/getting-started/basic-tutorial/) and NuGet's special ODATA extensions. The blog article [Fun With NuGet Web Api](http://chris.eldredge.io/blog/2013/02/25/fun-with-nuget-rest-api/) shows some examples of how to use this API.

In addition, here are a few common queries you may find helpful:

**Listing all packages of a feed in JSON format**

```bash
https://«proget-server»/nuget/«feed-name»/Packages?$format=json
```

**Manually downloading packages from a ProGet feed**

```bash
https://«proget-server»/«feed-name»/«packageName»/«versionNumber[optional]»
```

**ODATA package queries**

```bash
https://«proget-server»/nuget/{feed-name}/Packages?$filter=Id%20eq%20'«package-name»'
```

**Get Latest Version number of a package**

```bash
https://«proget-server»/nuget/{feed-name}/Packages()?$filter=Id%20eq%20%27«package-name»%27%20and%20IsAbsoluteLatestVersion&$top=1
```

### NuGet.org and the ODATA (V2) API

:::(Warning)
NuGet.org deprecated certain V2 ODATA queries, which will lead to an error if you have a connector to NuGet.org and query the feed with these queries. See  [Problems With My NuGet.org Connectors
](/docs/proget/feeds/nuget/problems-with-my-nuget-org-connectors) to learn more.
:::

Because the  ODATA (v2) API has limited support on NuGet.org, ProGet will always use the JSON-LD (v3) endpoint ( `https://api.nuget.org/v3/index.json`) if the connector host name is nuget.org. You can add an override token (`#v2`) at the end of a nuget.org URL, such as `http://nuget.org/api/v2#v2`, to force the ODATA (v2) protocol.

## Legacy (Quirks) NuGet Feeds 

NuGet feeds created before ProGet 5.0 are considered legacy NuGet (Quirks) feeds. The Quirks feed type uses the loose semantic versioning rules that NuGet.org has historically favored. In ProGet 5.0, new NuGet feeds now use a more sophisticated versioning scheme that takes into account the Semantic Versioning 2.0 rules for pre-release version strings. However, versions that violate the SemVer2 specification will still be displayed in SemVer2 feeds.

To migrate a legacy NuGet (Quirks) feed, click the Migrate link on the Manage Feed page. ProGet opens a confirmation dialog with a set of migration options. When performing a migration, ProGet performs the following steps:

- Set the feed to `disabled` state
- Add new metadata (Database) entries to the feed
- Remove old (Quirks) metadata for the feed
- Reactivate the feed

This migration is strictly a database operation that does not require your packages to be changed at all. By default, the migration is performed as a single database transaction, so it can be undone in the unlikely event of an error. If you have a very large number of packages in your feed (> 10,000), we strongly recommend that you back up the ProGet SQL database before performing the migration and disable this single transaction mode by clearing the corresponding check box in the Migrate Feed dialog.

Legacy (Quirks) NuGet Feeds were removed in ProGet 5.3, and upgrading will be blocked unless you've migrated or deleted all of these feeds.

### Legacy NuGet API Key 

NuGet (Quirks) feeds supported an API key definition directly on the Manage Feed page; these keys will be removed when upgrading to ProGet 5.3.

Legacy NuGet API keys behave slightly differently than ProGet API keys: if a legacy key is defined for the feed, it is checked *in addition* to the authenticated user. If the same key is defined as both a legacy key and a ProGet key, the ProGet key takes precedence.

