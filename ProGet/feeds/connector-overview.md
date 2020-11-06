---
title: What is a "Connector" in ProGet?
subtitle: Connectors in ProGet
sequence: 60
keywords: connectors, filters, licensing
show-headings-in-nav: true
---

Connectors are at the core of ProGet’s functionality, unlocking most other ProGet features, including [license filters](/docs/proget/compliance/license-detection), [vulnerability scanning and blocking](/docs/proget/compliance/vulnerabilities), [promotion](/docs/proget/packages/package-promotion), and of course using multiple feeds.

Connectors allow ProGet feeds to include packages from an external source, whether it is another ProGet feed, a public gallery like NuGet.org, or another third-party package source like Azure DevOps Packages or [Artifactory](https://inedo.com/proget/proget-vs-jfrog-artifactory).

This means you can:

1. aggregate/combine contents from multiple feeds, so you don’t have to hunt across feeds for what you need.
2. bypass direct/raw access to open-source feeds, filtering out unwanted packages using [license detection](/docs/proget/compliance/license-detection) and [vulnerability scanning](/docs/proget/compliance/vulnerabilities) and allowing only quality packages to be used in production feeds via [promotion](/docs/proget/packages/package-promotion).
3.	access the packages you need from a third party, even if firewall restrictions prevent you from going to third-party websites like npmjs.org.
4.	cache packages and metadata locally to eliminate wait times to re-download a package.

ProGet checks connector health once per hour to determine whether a connector URL is still accessible and the number of packages in a remote feed.

## ProGet Free Edition Limitations {#free-edition-limitations data-title="Limitations in Free"}

ProGet Free may only connect to public repositories (e.g., nuget.org, registry.npmjs.org) or [between feeds in the same ProGet instance](/docs/proget/feeds/connector-overview#self-connectors), but you will not be able to filter unwanted packages for inappropriate licenses, security vulnerabilities, or known-quality.

Connecting to another ProGet instance is not permitted and could result in license violation notifications being displayed on both instances. See [ProGet Free Edition Limitations](/docs/proget/administration/license#proget-free-edition-limitations) for more information about the specific restrictions that apply to connectors. Many ProGet users combine paid and free ProGet licenses to use connectors between different ProGet instances.

## Feed Association {#feed-association data-title="Feed Association"}

After a connector is created, it must be associated with one or more feeds before packages can be retrieved from the connector. To do this, click **add connector** on the **Manage Feed** page and select the connector. Packages from the connector will immediately become available in the feed.

Starting in ProGet 5.3, connectors are created within a specific feed and are then automatically associated with that feed. 

If you need to change the connector's associated feed:

1. navigate to the "Manage Feed" page.
2. select the "Connectors & Replication" tab.
3. click the X to disassociate the connector with the feed.

You can then add the connector to a different feed from that feed's "Manage Feed" page. You can delete a connector entirely from the Connectors screen (Feeds > Connectors). 

## Self-Connectors {#self-connectors data-title="Self Connectors"}

Self-connectors are connectors that refer to the same instance of ProGet. These may be useful to:

 - aggregate feeds specific to different teams or organizations, storing packages in a single place
 - apply a different set of permissions to a feed, for example to limit access to pre-production feeds
 - filter packages from a different feed, for example to indicate quality

{.attention .technical} Self-connectors, despite being local to the same instance, will still forward any web requests as if it were non-local. This means that users with Integrated Windows Authentication enabled must configure either the ProGet Integrated Web Server logon user (or application pool identity if using IIS) to run as an account with permissions to access the connected feed.

The URL needed to create a self-connector can be found on the "Feed Overview" page for the source feed.

## Connector Caching {#connector-caching data-title="Connector Caching"}

ProGet provides two types of connector caching: package and metadata. Caching packages and metadata in ProGet reduces queries and lowers wait times, as packages are already in ProGet.

**Package Caching** is configured at the feed level. When enabled, package caching saves a copy of every connector package that is downloaded in the associated feed. The package will still appear as a connector package in the UI, but it will be available even if the external package source is unreachable. Package caching is enabled by default and is included in all editions of ProGet.

**Metadata Caching** is configured at the connector level. When enabled, metadata caching stores responses of recently used queries to the connector's source. For example, a tool may query a ProGet feed for the latest version of a package, so ProGet must then query each of the feed's connectors for the latest versions. With metadata caching enabled, this can significantly increase performance. However, this comes at the cost of potentially stale queries. Metadata caching is available only in paid ProGet editions.

## Connector Filters {#connector-filters data-title="Connector Filters"}

Connector filters help you curate third-party packages by automatically including trusted package names (such as "JQuery" or "Microsoft.* ") and blocking unacceptable package names. This means you don't have to approve every package (and version) as they are published. 

This feature is available in paid and trial ProGet editions. Connector Filters may be configured in ProGet Free, but they will be ignored. {.attention .best-practice}

To add a connector filter:

1. navigate to the "Manage Feed" page.
2. select the "Connectors & Replication" tab.
3. select the desired connector name.
4. click "Add Filter," enter the filter specifics and selecting "Allow" or "Block."

