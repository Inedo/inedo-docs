---
title: NuGet (.NET)
subtitle: NuGet Packages and Feeds in ProGet
sequence: 100
keywords: proget,feeds,nuget,
show-headings-in-nav: true
---

NuGet is a package format developed by Microsoft to distribute free and open-source .NET libraries. Typically, these packages are publicly available on NuGet.org, and are consumed by Visual Studio or the nuget.exe command-line client.

{.docs}
- [NuGet Visual Studio Tutorial](/support/tutorials/proget/using-the-nuget-visual-studio-extension)
- [NuGet command-line Reference](https://docs.microsoft.com/en-us/nuget/tools/nuget-exe-cli-reference)

With ProGet, you can use NuGet packages to distribute private proprietary libraries within your organization. To do this, simply create a NuGet feed: Feeds > Create New Feed > Type = NuGet.

Then [configure Visual Studio](/support/tutorials/proget/using-the-nuget-visual-studio-extension) to use your feed as a new package source.

You can use also ProGet as a proxy to NuGet.org, both to block [improper licenses](/support/documentation/proget/compliance/license-scanning) and [security vulnerabilities](/support/documentation/proget/compliance/vulnerabilities), and to cache packages in the event of an internet or NuGet.org outage. To do this, create a connector (Feeds > Connectors > Create New Connector > NuGet), then associate it with a feed (Manage Feed > Connectors).

### Installing Packages

NuGet packages are installed using [NuGet CLI](https://docs.microsoft.com/en-us/nuget/quickstart/install-and-use-a-package-using-the-dotnet-cli). To install a package from a ProGet feed, use the following command:

```
nuget install {package-name} -Version {package-version} -Source http://{proget-server}/nuget/{feed-name}/
```


## Creating and Publishing NuGet Packages {#creating data-title="Creating and Publishing"}

The [NuGet package format](https://docs.microsoft.com/en-us/nuget/schema/nuspec) is well-documented, and you can create packages in a number of ways, including [directly from Visual Studio](https://docs.microsoft.com/en-us/nuget/guides/create-net-standard-packages-vs2017) or using [nuget.exe](https://docs.microsoft.com/en-us/nuget/create-packages/creating-a-package). Once you've created a package, there are four ways you can publish it to ProGet:

{.docs}
- Upload from Disk - upload a pre-packaged NuGet package (.nupkg) from disk
- Push via NuGet Command Line Utility - uses the nuget.exe command line to add a package to the local feed
- Pull from another Feed - pulls a package from another NuGet feed (such as nuget.org). Note: this feed URL must be a NuGet v2 URL at this time, e.g. `https://nuget.org/api/v2`
- Bulk Package Import/File Copy - import multiple existing packages at once

For more specific instructions on how to add a package to a feed, click the `Add Package` button on your NuGet feed.

## NuGet API Key {#nuget data-title="NuGet API Key"}

NuGet client tools (such as nuget.exe and NuGet Package Explorer) have the option to use a "NuGet API Key" as an authentication mechanism for publishing packages. This makes a lot of sense for the NuGet Gallery, as it's a community-run site where anyone can publish packages they own. But in enterprise environments, the notion of "individual package ownership" doesn't quite translate.

With ProGet, a principal (user or group) either has privileges to publish a package to a feed, or does not. This allows ProGet administrators to more easily control access and usage through LDAP and Groups instead of through community mechanisms like API Keys. However, if you wish to use a NuGet API key when pushing to ProGet, you have two options:

{.docs}
- **username:password as API Key** - ProGet will allow users to authenticate in this mode using an API Key in the format: `username:password`. For example, you can push a package to a Feed with the default Admin account in ProGet using the `-ApiKey Admin:Admin` option with nuget.exe
-   **[Create ProGet API Key](/support/documentation/proget/administration/security/api-keys)** - you can create an API Key in ProGet (Admin &; API Key &; Access Logs) with Feed Access; you can further restrict this key by associating it to a user you've already given specific permissions

### Legacy NuGet API Key {#legacy}

NuGet feeds also support an API key definition directly on the Manage Feed page. This is a deprecated legacy feature, so it is recommended to migrate to a ProGet API Key at your earliest convenience.

Legacy NuGet API Keys behave slightly differently than ProGet API Keys: If a legacy key is defined for the feed, it will be checked *in addition to* the authenticated user. In the event the same key is defined both as a legacy key and as a ProGet key, the ProGet key will take precedence.

## Symbols and Source Packages {#symbols}

A NuGet feed in ProGet may be configured as a Symbol/Source server compatible with debuggers such as Visual Studio and WinDbg. See [Symbols and Source Code in ProGet](/support/documentation/proget/feeds/nuget/symbol-and-source-server) for more information.

## Using the NuGet API {#using data-title="Using the NuGet API"}

ProGet implements the NuGet ODATA protocol. Because this is a third-party API, we don't provide documentation for their API. However, here are a few places to get started:

{.docs}
- [ODATA Overview](http://www.odata.org/getting-started/basic-tutorial/)
- [Fun With NuGet Web Api](http://chris.eldredge.io/blog/2013/02/25/fun-with-nuget-rest-api/)

In addition, here are a few common queries you may find helpful:

**Listing all packages of a feed in JSON format**

```
http://proget-server/nuget/{feedName}/Packages?$format=json
```

**Manually downloading packages from a ProGet feed**

```
http://proget-server/api/v2/package/{feedName}/{packageName}/{optional-version}
```

**ODATA package queries**

```
http://proget-server/nuget/{feedName}/Packages?$filter=Id%20eq%20'jQuery'
```

### NuGet V3 API {#nuget-v3 data-title="NuGet V3 API"}

Both NuGet.org and the NuGet client tools support two different API protocols: ODATA (v2) and JSON-LD (v3). Microsoft plans to maintain and support both APIs indefinitely, to support older versions of the NuGet clients and the dozens of third-party tools (like ProGet) that use the ODATA protocol.

There is no need or benefit to having ProGet support the JSON-LD protocol. It  is extremely complex, as it is designed to solve the specific needs of NuGet.org: a massive, publicly-available static package index. Because ProGet has connectors, authentication, package filtering, etc., it's not feasible or useful to generate a static index.

However, it is on our 2019 roadmap to implement this API, possibly as a new feed type or as an alternate API endpoint for an existing feed.

## Legacy (Quirks) NuGet Feeds {#legacy data-title="Legacy (Quirks) NuGet Feeds"}

NuGet feeds created prior to ProGet 5.0 are considered Legacy (Quirks) NuGet feeds. The quirks feed type uses the loose semantic versioning rules that NuGet.org preferred in the past. In ProGet 5.0, new NuGet feeds now use a more sophisticated versioning scheme that accommodates semantic versioning 2.0 rules for pre-release version strings. However, versions that violate the SemVer2 specification will still appear in SemVer2 feeds.

To migrate a legacy (quirks) NuGet feed, click the "migrate" link on the manage feed page. ProGet will pop up a confirmation dialog with a couple migration options. When executing a migration, ProGet will do the following:

{.docs}
- Set the feed to disabled state
- Add new metadata (Database) entries to the feed
- Remove old (quirks) metadata for the feed
- Reactivate the feed

This migration is entirely a database operation and does not need to manipulate your packages at all. By default the migration is performed as a single database transaction so that it may be rolled back in the unlikely event of a failure. If you have a very large number of packages in your feed (>10,000), we strongly recommend performing a backup of the ProGet SQL database before executing the migration and disabling this single-transaction mode by unchecking the appropriate box on the "migrate feed" dialog.

## NuGet Feed Re-Indexing {#re-index data-title="NuGet Feed Re-Indexing"}

NuGet feed re-indexing in ProGet was originally designed to re-index the [symbol and source server](/support/documentation/proget/feeds/nuget/symbol-and-source-server) in SemVer2 (i.e. non-legacy) feeds. As of ProGet v5.0, it has been extended to perform the following additional actions:

{.docs}
 - fix inconsistencies related to pre-release versioning and build metadata
 - remove local or cached packages from the database that are no longer in a package store
 - fix database-indexed .nuspec data from a package

To perform a NuGet feed re-index, visit the "Manage Feed" page of the NuGet, Chocolatey, or PowerShell feed, and select `re-index` under the Symbol Server feed property section.