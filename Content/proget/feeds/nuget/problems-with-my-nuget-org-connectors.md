---
title: "Problems With My NuGet.org Connectors"
order: 5
---

This article applies to NuGet.org [deprecating API features](https://devblogs.microsoft.com/nuget/custom-v2-odata-queries-will-be-deprecated-march-9-2021/) on **March 9, 2021**, and may help explain problems with connectors as a result.

### My NuGet feed is suddenly empty and not showing remote packages

If you configured a connector to NuGet.org in ProGet 5.2 or earlier, it may suddenly stop working and not return any packages. This is due to an API change in NuGet.org, and the only solution is to upgrade to ProGet 5.3 or later. You can also work around the problem temporarily by [disabling your connector filters](/docs/proget/feeds/connector-overview#connector-filters).

### My NuGet connector shows healthy, but it has the wrong package count

If you configured a connector to NuGet.org in ProGet 5.2 or earlier, you may notice a healthy connector status but an incorrect package count. NuGet.org has deprecated its previous queries to check the status and count of NuGet packages because they are very taxing on the NuGet servers. In the future, NuGet.org will display a fixed number of packages when health status is queried.

## NuGet API Background

NuGet is a package format developed by Microsoft to distribute free and open-source .NET libraries. Usually, these packages are publicly available on NuGet.org and are used by Visual Studio or the command line client nuget.exe.

When NuGet was released, a client/server protocol was needed for users to query packages, retrieve package metadata, and download package artifacts. The logical choice at the time was [OData](http://www.odata.org): by exposing their Entity Framework model via OData, the NuGet client could run any query on the database, using OData as the protocol. The problem with this architecture is that every query is a direct database query. This caused significant performance issues with NuGet.org, causing the server to crawl under heavy load.

In early 2016, NuGet introduced the third version of its API to dramatically improve the overall performance of NuGet.org.

In late 2019, NuGet announced that they would begin deprecating features of their API v2 to make it a stable and performant shim on API v3, ensuring that the data returned in both APIs is identical without breaking backward compatibility. The first step was to throttle the usage of API v2. The second step of this process is to eliminate non-performing queries that do not match API v3 on a 1:1 basis.

On March 9, 2021, NuGet.org began blocking select endpoints used by third-party clients such as ProGet. For more information, Microsoft's blog article [Custom V2 OData queries will be deprecated on March 9, 2021](https://devblogs.microsoft.com/nuget/custom-v2-odata-queries-will-be-deprecated-march-9-2021/).

## How Does This Affect ProGet?

If you have already upgraded to ProGet 5.3+, you are not directly affected, but we recommend that you make sure that the v3 API is enabled in ProGet on the feed Manage Feed page, and configure your NuGet clients (and Visual Studio) to use the new v3 API URL for your feeds.

In ProGet 5.3, we introduced the implementation of the NuGet v3 API and gave connectors the ability to use the v3 API to connect to NuGet.org. ProGet 5.3 is also backward compatible with the NuGet v2 API. This means that any v2 client that connects to ProGet to query, push, pull, etc. will not be affected as long as those packages are local or their connector uses the v3 API. If you are still using a v2-based connector, ProGet 5.3 (via [PG-1847](https://issues.inedo.com/youtrack/issue/PG-1847)) will display a message if an unsupported query is used. Unsupported queries can be viewed in the Diagnostics Center under the "NuGet v2 Bad Requests" message category. This message can be turned off from the Manage Feed page of the respective feed.

If you are using ProGet 5.2 or lower, you will notice that some features of the ProGet connectors no longer work (see above). Nothing will change in your local packages, they will continue to work as before. The state of the connectors may be displayed correctly, but the total number of NuGet packages may not be the correct number of packages.
