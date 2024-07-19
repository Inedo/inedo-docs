---
title: "What is a \"Connector\" in ProGet?"
order: 2
---


Connectors are at the core of ProGet’s functionality and unlock most other ProGet features, including [license filters](/docs/proget/sca/licenses){target="_blank"}, [vulnerability scanning and blocking](/docs/proget/sca/vulnerabilities){target="_blank"}, [promotion](/docs/proget/packages/package-promotion){target="_blank"}, and of course, the use of multiple feeds.

Connectors allow ProGet feeds to include packages from an external source, whether it is another ProGet feed, a public gallery like NuGet.org, or another third-party package source like Azure DevOps Packages or [Artifactory](https://inedo.com/proget/proget-vs-jfrog-artifactory).

This means you can:

1. aggregate/combine content from multiple feeds, so you don't have to search in different feeds for what you need.
2. bypass direct/raw access to open-source feeds, filtering out unwanted packages using [license detection](/docs/proget/sca/licenses) and [vulnerability scanning](/docs/proget/sca/vulnerabilities) to allow only quality packages to be used in production feeds via [promotion](/docs/proget/packages/package-promotion).
3.	access the required third-party packages, even if firewall restrictions prevent access to third-party sites such as npmjs.org.
4.	cache packages and metadata locally to avoid waiting when re-downloading a package.

ProGet checks the state of the connector once an hour to determine if a connector URL is still accessible and the number of packages in a remote feed.

## ProGet Free Edition Limitations 

ProGet Free can only connect to public repositories (e.g. nuget.org, registry.npmjs.org) or [between feeds in the same ProGet instance](/docs/proget/feeds/connector-overview#self-connectors), but you cannot filter unwanted packages for inappropriate licenses, security vulnerabilities, or known-quality.

Connecting to another ProGet instance is not permitted and may result in license violation notifications being displayed on both instances. See [ProGet Free Edition Limitations](/docs/proget/administration/license#proget-free-edition-limitations) for more information on the specific restrictions that apply to connectors. Many ProGet users combine paid and free ProGet licenses to use connectors between different ProGet instances.

## Feed Association 

After a connector is created, it must be associated with one or more feeds before packages can be retrieved from the connector. To do this, click **add connector** on the **Manage Feed** page and select the connector. Packages from the connector are then immediately available in the feed.

Starting with ProGet 5.3, connectors are created within a specific feed and are then automatically connected to that feed.

If you need to change the feed associated with the connector:

1. navigate to the "Manage Feed" page.
2. select the "Connectors & Replication" tab.
3. click the X to disassociate the connector from the feed.

You can then add the connector to another feed from the Manage Feed page. You can delete a connector completely from the Connectors screen (Feeds > Connectors ).

## Self-Connectors 
Self connectors are connectors that refer to the same instance of ProGet. These can be useful to:

 - aggregate feeds for different teams or organizations, and store packages in a single location
 - apply a different set of permissions to a feed, for example, to restrict access to pre-production feeds
 - filter packages from another feed, e.g., to view quality
 
:::(Error) 
Self-connectors are locally connected to the same instance, but still forward all web requests as if they were not local. This means that users with built-in Windows authentication enabled must configure either the ProGet Integrated Web Server login user (or the application pool identity if using IIS) to run as an account with access rights to the connected feed.
:::


The URL needed to create a self-connector can be found on the "Feed Overview" page for the source feed.

## Connector Caching 

ProGet provides two types of connector caching: package and metadata. Caching packages and metadata in ProGet reduces queries and decreases wait times as packages are already in ProGet.

**Package Caching** is configured at the feed level. When enabled, a copy of each Connector package downloaded in the associated feed is cached. The package will still show up as a Connector package in UI, but it will be available even if the external package source is unreachable. Package caching is enabled by default and included in all editions of ProGet.

**Metadata Caching** is configured at the connector level. When enabled, metadata caching stores responses from recently used queries to the connector's source. For example, a tool may query a ProGet feed for the latest version of a package, so ProGet must then query each of the connectors in the feed for the latest versions. With metadata caching enabled, this can significantly increase performance. However, this has the disadvantage that the queries may become stale. Metadata caching is only available in paid ProGet editions.

## Connector Filters 

Connector filters help you select third-party packages by automatically including trusted package names (such as "JQuery" or "Microsoft.* ") and blocking unacceptable package names. This means you do not have to approve every package (and version) when it is released.

:::(Info)
This feature is available in the paid and trial versions of ProGet. Connector filters can be configured in ProGet Free, but are ignored.
:::


How to add a connection filter:

1. navigate to the "Manage Feed" page.
2. select the "Connectors & Replication" tab.
3. select the name of the desired connector.
4. click "Add Filter", enter the filter specifications and select "Allow" or "Block"
