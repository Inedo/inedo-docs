---
title: Feeds
subtitle: Feeds in ProGet
sequence: 200
keywords: proget,feeds
show-headings-in-nav: true
---

:::attention {.analogy}
Think of a feed like a private 'app store', but for your own applications and components. Users can easily see what's available and download desired packages, and old and unused packages can be automatically cleaned.
:::

You can control who can access which feeds, and assign granular permissions for performing specific tasks on a feed. For example, you could allow everyone to view and download packages on all feeds, but only allow ".NET Developers" to publish packages to the "NuGet-Dev" feed.

By setting up multiple feeds, you can not only separate packages by package format and development group, but also use [package promotion](/support/documentation/proget/advanced/package-promotion) to identify quality and compliant packages.

## Creating and Managing Feeds {#creating data-title="Creating and Managing Feeds"}

To create a new feed, go to `Feeds > Create Feed`. You can also manage feeds from the Feeds page, or by going to the specific feed page and clicking the `Manage Feed` button.

### Feed Types {#feed data-title="Feed Types"}

The type of feed defines the format of package that it will contain. A NuGet feed, for example, will only ever store NuGet packages, and a npm feed will only ever store npm packages.

The type also determines the API that is used to programmatically access packages within that feed. For example, a Universal feed will only use the [Universal Feed API](/support/documentation/upack/feed-api), and a NuGet feed will only ever use the NuGet API.

### Package File Storage {#package data-title="Package File Storage"}

By default, a feed's package files are stored on disk, in a directory managed by ProGet. You can change this directory by either editing the default directory, or by setting the Disk Path property on a feed.

:::attention {.analogy}
To use cloud storage, you need to configure a different package store; see [Amazon S3 and Azure Blob Package Storage](/support/documentation/proget/advanced/cloud-storage) for more information.
:::

Accessing the package files directly for any reason other than backing up files is not supported. Future versions of ProGet may change the way package files are stored on disk.

#### Default Directory

A feed's default directory is built by combining the following path elements:

{.docs}
- **Root Storage Path** - this path is configured in the `Storage.PackagesRootPath` setting (`Admin > Advanced Settings`), and defaults to `%ProGramData%\ProGet\PackagesRoot`. Its value may be assigned in Advanced Settings or, for load-balanced installations, may be set in the installation's .config file (web.config or ProGet.Service.config) with a key name of `ProGetConfig.Storage.PackagesRootPath`. To use the .config file value, the value specified here must be saved with an empty value. When configured in the .config file, that value will be displayed here.
- **Package Type Path** - each feed type uses a different setting and a different default. For example, npm feeds use `Storage.NpmPackagesLibrary`, which default to `.npm`. *Note: all SemVer2 NuGet feed types (including Chocolatey and PowerShell) are currently fixed in `.nugetv2` *
- **FeedId.** The immutable, integer-based identifier for a feed

If the package type path is an absolute path, then the root storage path will be ignored. For example, assuming the default settings weren't changed, a NuGet feed with an ID of 1 would store packages in the following path:

```
%ProgramData%\ProGet\PackagesRoot\.nuget\F1
```

#### Changing Directories {#changing data-title="Changing Directories"}

When you change a feed's disk directory, either directly (by going to the feed setting page) or indirectly (by editing the path defaults), the package files will not be moved.

##### Access & Permissions {#permissions}

Both the ProGet web application (either the logon user for the `INEDOPROGETWEBSVC` service for the Integrated Web Server, or the ProGet IIS site's associated application pool identity) and `INEDOPROGETSVC` service logon user must have full control permissions on a feed's disk directory.

When using network storage, you should use the UNC path (e.g. `\\\filesv1\packages`) as opposed to mapped drive (`P:\packages`). This is because of the way mapped drives work in Windows; they can be a bit tricky to configure and keep working for service accounts.

### Drop Path and Bulk Package Importing {#drop data-title="Drop Path and Bulk Package Importing"}

In some cases, it may be easier for users or programs to simply copy package files to a network drive rather than use that feed type's API to publish it to ProGet. One common scenario for this is a bulk import of packages.

You can configure a feed to use a "drop path", which is a local- or network path that ProGet will periodically scan for files. If that directory contains a package with a valid format for that feed type, it will be imported into ProGet.

By default, existing packages will not be overwritten, but you can change this by going to `Admin > Advanced Settings`, and changing `Feeds.AllowDropPathImportOverwrite` to true.

### Connectors

Connectors allow ProGet feeds to include packages from an external source, whether it is another ProGet feed, a public feed, or any other implementer of the package API for the feed type. See the [connectors documentation](/support/documentation/proget/core-concepts/feeds/connectors) for more information about connectors.

### Vulnerability Sources

ProGet has integrations with two leading vulnerability scanning companies to automatically scan third-party packages against vulnerability databases. See [Vulnerability Scanning and Blocking](/support/documentation/proget/compliance/vulnerabilities) for more information.

### License Filters 

ProGet offers two workflows for managing licensing agreements of third-party, open-source packages. See [License Scanning and Blocking](/support/documentation/proget/compliance/license-scanning) for more information.

### Retention Rules 

Retention rules help you reclaim disk space by automatically deleting old or unused packages that meet a set of criteria that you define. See [Retention Rules](/support/documentation/proget/administration/retention-rules) for more information.

### Variables 

Variables are key/value pairs that can be used in [Webhooks](/support/documentation/proget/advanced/webhooks) to allow you to integrate ProGet with other tools and  notify users, trigger automated workflows, or audit events like package creation and deletion.

## Feed API Endpoints {#feed data-title="Feed API Endpoints"}

All feeds have an "API Endpoint" URL that is used for API (programmatic) access. You can find this URL in the top-right corner of the Feed's browse page. The specific API that a feed uses depends entirely on its type; for example, a Universal feed will only use the [Universal Feed API](/support/documentation/upack/feed-api), and a NuGet feed will only ever use the NuGet API.

## Deleting a Feed {#deleting data-title="Deleting a Feed"}

A feed can either be deactivated or deleted. Deactivating a feed will make it invisible from most of the user interface, and inaccessible from API endpoints.

Deleting a feed will remove it entirely from the software. All package data indexed in the database will be deleted, as well as metadata not stored in the package itself, such as publish dates and download counts for each package.

{.attention .technical} Packages themselves are not deleted when deleting a feed. If you would like to delete them, make note of the feed disk path, and delete them manually after deleting the feed.
