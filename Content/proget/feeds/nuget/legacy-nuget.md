---
title: "Legacy NuGet Feeds and API in ProGet"
order: 8
---

As part of ProGet's package management system, support for older technologies like the NuGet ODATA (v2) API and Legacy NuGet (Quirks) feeds remains for certain use cases. 

In this article, we’ll cover:
* The NuGet ODATA (v2) API for custom queries
* Management and migration of Legacy (Quirks) NuGet feeds.
* Troubleshooting NuGet Connectors

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
NuGet.org deprecated certain V2 ODATA queries, which will lead to an error if you have a connector to NuGet.org and query the feed with these queries. See [Troubleshooting: NuGet.org Connectors](/docs/proget/feeds/nuget#nuget-connectors) to learn more.
:::

Because the  ODATA (v2) API has limited support on NuGet.org, ProGet will always use the JSON-LD (v3) endpoint (`https://api.nuget.org/v3/index.json`) if the connector host name is nuget.org. You can add an override token (`#v2`) at the end of a nuget.org URL, such as `http://nuget.org/api/v2#v2`, to force the ODATA (v2) protocol.

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

### NuGet API Background

NuGet is a package format developed by Microsoft to distribute free and open-source .NET libraries. Usually, these packages are publicly available on NuGet.org and are used by Visual Studio or the command line client nuget.exe.

When NuGet was released, a client/server protocol was needed for users to query packages, retrieve package metadata, and download package artifacts. The logical choice at the time was [OData](http://www.odata.org): by exposing their Entity Framework model via OData, the NuGet client could run any query on the database, using OData as the protocol. The problem with this architecture is that every query is a direct database query. This caused significant performance issues with NuGet.org, causing the server to crawl under heavy load.

In early 2016, NuGet introduced the third version of its API to dramatically improve the overall performance of NuGet.org.

In late 2019, NuGet announced that they would begin deprecating features of their API v2 to make it a stable and performant shim on API v3, ensuring that the data returned in both APIs is identical without breaking backward compatibility. The first step was to throttle the usage of API v2. The second step of this process is to eliminate non-performing queries that do not match API v3 on a 1:1 basis.

On March 9, 2021, NuGet.org began blocking select endpoints used by third-party clients such as ProGet. For more information, Microsoft's blog article [Custom V2 OData queries will be deprecated on March 9, 2021](https://devblogs.microsoft.com/nuget/custom-v2-odata-queries-will-be-deprecated-march-9-2021/).

#### How Does This Affect ProGet?

If you have already upgraded to ProGet 5.3+, you are not directly affected, but we recommend that you make sure that the v3 API is enabled in ProGet on the feed Manage Feed page, and configure your NuGet clients (and Visual Studio) to use the new v3 API URL for your feeds.

In ProGet 5.3, we introduced the implementation of the NuGet v3 API and gave connectors the ability to use the v3 API to connect to NuGet.org. ProGet 5.3 is also backward compatible with the NuGet v2 API. This means that any v2 client that connects to ProGet to query, push, pull, etc. will not be affected as long as those packages are local or their connector uses the v3 API. If you are still using a v2-based connector, ProGet 5.3 (via [PG-1847](https://issues.inedo.com/youtrack/issue/PG-1847)) will display a message if an unsupported query is used. Unsupported queries can be viewed in the Diagnostics Center under the "NuGet v2 Bad Requests" message category. This message can be turned off from the Manage Feed page of the respective feed.

If you are using ProGet 5.2 or lower, you will notice that some features of the ProGet connectors no longer work (see above). Nothing will change in your local packages, they will continue to work as before. The state of the connectors may be displayed correctly, but the total number of NuGet packages may not be the correct number of packages.

## Troubleshooting: NuGet Connectors

The following applies to NuGet.org [deprecating API features](https://devblogs.microsoft.com/nuget/custom-v2-odata-queries-will-be-deprecated-march-9-2021/) on **March 9, 2021**, and may help explain problems with connectors as a result.

### My NuGet feed is suddenly empty and not showing remote packages

If you configured a connector to NuGet.org in ProGet 5.2 or earlier, it may suddenly stop working and not return any packages. This is due to an API change in NuGet.org, and the only solution is to upgrade to ProGet 5.3 or later. You can also work around the problem temporarily by [disabling your connector filters](/docs/proget/feeds/connector-overview#connector-filters).

### My NuGet connector shows healthy, but it has the wrong package count

If you configured a connector to NuGet.org in ProGet 5.2 or earlier, you may notice a healthy connector status but an incorrect package count. NuGet.org has deprecated its previous queries to check the status and count of NuGet packages because they are very taxing on the NuGet servers. In the future, NuGet.org will display a fixed number of packages when health status is queried.