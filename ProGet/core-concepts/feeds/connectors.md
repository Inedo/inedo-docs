---
title: Connectors
subtitle: Connectors in ProGet
sequence: 200
keywords: connectors, filters, licensing
---

Connectors allow ProGet feeds to include packages from an external source, whether it is another ProGet feed, or any other implementer of the package API for the feed type.

For example, a NuGet feed may include a connector to the official repository at nuget.org, which will make all of nuget.org's packages available from that feed. A connector can even refer to another feed within the same instance of ProGet; this is sometimes useful for limiting access.

ProGet will periodically check the health of each connector and report on the approximate number of packages in the external source.

![](/resources/documentation/proget/core-concepts-connectors/connectors-health.png)

**Note:** Packages accessed through connectors will appear as if they are ordinary
packages contained on the ProGet server to most third-party tools. Within the ProGet web interface, connector packages are displayed with a small overlay icon to indicate that they are external. A connector package can be *pulled* to ProGet, which will cause the package to be downloaded and stored on the ProGet server.

## Feed Association {#feed data-title="Feed Association"}

After a connector is created, it must be associated with one or more feeds before packages can be retrieved from the connector. To do this, click **add connector** on the **Manage Feed** page and select the connector. Packages from the connector will immediately become available in the feed.

![](/resources/documentation/proget/core-concepts-connectors/feed-connectors.png)

## Connector Caching {#connector data-title="Connector Caching"}

ProGet provides two types of connector caching: package and metadata.

**Package Caching** is configured at the feed level, and when enabled, saves a copy of every connector package that is downloaded in the associated feed. The package will still appear as a connector package in the UI, but it will be available even if the external package source is unreachable. Package caching is enabled by default and is included in all editions of ProGet.

**Metadata Caching** is configured at the connector level, and stores responses of recently-used queries to the connector's source. For example, a tool may query a ProGet feed for the latest version of a package, so ProGet must then query each of the feed's connectors for the latest versions. With metadata caching enabled, this can significantly increase performance, at the cost of potentially stale queries. Metadata caching requires ProGet Basic or Enterprise.

## Connector Filters {#connector data-title="Connector Filters"}

This feature is available in paid and trial ProGet editions. {.info}

Connector filters allow for simple wildcard include/exclude lists to be applied to all connector packages. Filters always refer to the package's unique name/ID, and multiple filters may be entered one-per-line. A filter starting with a "!" is an exclude filter, while all others are include filters.

![](/resources/documentation/proget/core-concepts-connectors/connectors-filters.png)

**Note:** Filter lists should be kept simple to avoid performance issues. To maintain a set of curated packages, consider using [Package Promotion](https://www.youtube.com/watch?v=0uVDUt0wMjM) with multiple feeds instead.
