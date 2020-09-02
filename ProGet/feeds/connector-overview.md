---
title: What is a "Connector" in ProGet?
subtitle: Connectors in ProGet
sequence: 60
keywords: connectors, filters, licensing
show-headings-in-nav: true
---

<style type="text/css">
.rubbish td:nth-child(2) { font-size: 12px; }
.rubbish img { max-width: 635px; }
</style>

Connectors allow ProGet feeds to include packages from an external source, whether it is another ProGet feed, or any other implementer of the package API for the feed type.

For example, a NuGet feed may include a connector to the official repository at nuget.org, which will make all of nuget.org's packages available from that feed. A connector can even refer to another feed within the same instance of ProGet; this is sometimes useful for limiting access.

ProGet will periodically check the [health](#health) of each connector and report on the approximate number of packages in the external source.

{.rubbish} ![](/resources/documentation/proget/core-concepts-connectors/connectors-health.png)

{.attention .analogy} **Note:** Packages accessed through connectors will appear as if they are ordinary packages contained on the ProGet server to most third-party tools. Within the ProGet web interface, connector packages are displayed with a small overlay icon to indicate that they are external. A connector package can be *pulled* to ProGet, which will cause the package to be downloaded and stored on the ProGet server.

## Feed Association {#feed-association data-title="Feed Association"}

After a connector is created, it must be associated with one or more feeds before packages can be retrieved from the connector. To do this, click **add connector** on the **Manage Feed** page and select the connector. Packages from the connector will immediately become available in the feed.

![](/resources/documentation/proget/core-concepts-connectors/feed-connectors.png)

## Connector Caching {#connector-caching data-title="Connector Caching"}

ProGet provides two types of connector caching: package and metadata.

**Package Caching** is configured at the feed level, and when enabled, saves a copy of every connector package that is downloaded in the associated feed. The package will still appear as a connector package in the UI, but it will be available even if the external package source is unreachable. Package caching is enabled by default and is included in all editions of ProGet.

**Metadata Caching** is configured at the connector level, and stores responses of recently-used queries to the connector's source. For example, a tool may query a ProGet feed for the latest version of a package, so ProGet must then query each of the feed's connectors for the latest versions. With metadata caching enabled, this can significantly increase performance, at the cost of potentially stale queries. Metadata caching requires ProGet Basic or Enterprise.

## Connector Filters {#connector-filters data-title="Connector Filters"}

This feature is available in paid and trial ProGet editions. {.attention .best-practice}

Connector filters allow for simple wildcard include/exclude lists to be applied to all connector packages. Filters always refer to the package's unique name/ID, and multiple filters may be entered one-per-line. A filter starting with a "!" is an exclude filter, while all others are include filters.

{.rubbish} ![](/resources/documentation/proget/core-concepts-connectors/connectors-filters.png)

{.attention .technical} **Note:** Filter lists should be kept simple to avoid performance issues. To maintain a set of curated packages, consider using [Package Promotion](https://www.youtube.com/watch?v=0uVDUt0wMjM) with multiple feeds instead.


## Connector Health {#health data-title="Connector Health"}

Connector health is checked once per hour, and used to determine whether a connector URL is accessible, and to indicate the number of packages in a remote feed. The following table outlines connector features and functionality:

{.rubbish}
| Feed          | Default URL                                               | Notes |
|---            |---                                                        |---    |
| NuGet         | `https://www.nuget.org/api/v2`                        |   -  |
| npm           | `https://registry.npmjs.org`                          |  _May_ be locally indexed ([more info](#local-index))  |
| Maven         | `https://repo1.maven.org/maven2`                      |   Locally indexed ([more info](#local-index)) |
| Chocolatey    | `https://chocolatey.org/api/v2`                       |   -  |
| PowerShell    | `https://www.powershellgallery.com/api/v2`           |   -  |
| Ruby Gems     | `https://api.rubygems.org`                             |  -  |
| UPack         | _no default_                                              |  -  |
| VSIX          | _no default_                                              |  Not required as VS force-searches its own gallery in addition to any custom one  |
| Docker        | `https://registry.hub.docker.com`                     |  Package count `unknown` displayed for large repos that do not serve `/v2/_catalog` to spec |
| PyPI          | `https://pypi.org`                                      |  -  |
| Debian        | -                                                         |  Debian feeds do not support connectors  |
| Bower         | `https://registry.bower.io/packages`                  |  -  |
| Helm          | `https://kubernetes-charts.storage.googleapis.com/` |   -  |
| Asset         | -                                                         | Asset directories do not support connectors |



### Connector Health API

Connector health may be queries as part of the ProGet API. Visit the [Connector Health API](/docs/proget/reference/api/connector-health-api) documentation for more information.

### Local Indexes {#local-index}

Most connectors allow transparent forwarding to the connector URL to query metadata. The following feeds however use local indexes:

 - **npm _(pre-v4.7.8 of ProGet)_:** connectors required a local index as they were created before the search API existed. The (now deprecated) `FullNpmConnectorIndex` and `IncrementalNpmConnectorIndex` scheduled jobs still exist in ProGet, but only for backwards compatibility with third-party repositories that still do not support the `v1/search` API
 - **maven:** uses a local Lucene database ([more info](/docs/proget/feeds/maven)) - this indexing is performed on a schedule, to trigger the index task manually visit the `Admin` > `Scheduled Jobs` page and select `FullMavenConnectorIndex`. If this connector index is not available, visit the `Admin` > `Service` page and trigger the `Scheduled Job Dispatcher` which will re-configure any missing connector index scheduled jobs

## Self-Connectors {#self-connectors data-title="Self Connectors"}

Self-connectors are defined as connectors that refer to the same instance of ProGet. These may be useful to:

 - aggregate feeds specific to different teams or organizations
 - apply a different set of permissions to a feed
 - filter packages from a different feed

{.attention .technical} Self-connectors, despite being local to the same instance, will still forward any web requests as if it were non-local. This means that users with Integrated Windows Authentication enabled must configure either the ProGet Integrated Web Server logon user (or application pool identity if using IIS) to run as an account with permissions to access the connected feed.

## ProGet Free Edition Limitations {#free-edition-limitations data-title="Free Edition Limitations"}

ProGet Free may only connect to public repositories (e.g. nuget.org, registry.npmjs.org), and connecting to another ProGet instance is not permitted and could result in license violation notifications being displayed on both instances. See [ProGet Free Edition Limitations](/docs/proget/administration/license#proget-free-edition-limitations) for more information about the specific restrictions that apply to connectors.
