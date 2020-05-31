---
title: What is a "Feed" in ProGet?
subtitle: Feeds in ProGet
sequence: 10
keywords: proget,feeds
show-headings-in-nav: true
---



A feed is like a private 'app store', but for your own applications and components. They are used to store packages, container images, and other assets, and users can easily see what's available to download what they need. 

You can control who can access which feeds, and assign granular permissions for performing specific tasks on a feed. For example, you could allow everyone to view and download packages on all feeds, but only allow ".NET Developers" to publish packages to the "NuGet-Dev" feed.

You can also configure retention policies (to clean up old/unused packages), scan feeds for vulnerabilities, detect license violations, and more.

By setting up multiple feeds, you can not only separate packages by package format and development group, but also use [package promotion](/docs/proget/advanced/package-promotion) to identify quality and compliant packages.

## Creating and Managing Feeds {#creating data-title="Creating and Managing Feeds"}

To create a new feed, go to `Feeds > Create Feed`. You can also manage feeds from the Feeds page, or by going to the specific feed page and clicking the `Manage Feed` button.

### Feed Name

A feed's name becomes a part of it's [API Endpoint URL](#api-endpoints); this means you can only use URL-friendly characters and that renaming a feed will also change its API Endpoint URL.

As of ProGet 5.2.19, you can create "Alternate Names" for a feeds by going to Manage Feed > Rename, then clicking the Set Alternate Names link in the warning dialog. Alternate feed names essentially provide multiple endpoint URLs for a feed, and are useful when renaming feeds to keep backwards compatibility with old names.

### Feed Types & Usage {#feed-types data-title="Feed Types & Usage"}

When you create a feed, the feed type is fixed. This type defines two things:

 * **Package Format the feed will use**; a NuGet feed, for example, will only ever store NuGet packages, and a npm feed will only ever store npm packages.
 * **API used to programmatically access packages within that feed**; for example, a Universal feed will only use the [Universal Feed API](/docs/upack/feed-api), and a NuGet feed will only ever use the NuGet API.

Some feeds have additional options that impact what is shown in the web user interface, such as icons and navigable options. 

* **NuGet package type**: Chocolatey and PowerShellGet both use NuGet packages, and by changing the package type, it changes the entire appearance of the feed
* **Feed usage**: this option is available in ProGet 5.3 and later, and the options are:
  * *Free/Open Source packages*; cache and filter source packages from public galleries, scan for vulnerabilities, check licenses, etc. 
  * *Private/Internal packages*; publish proprietary/private packages that you build for internal use in your organization 
  * *Validated/promoted packages*; promote packages that meet internal quality standards and are appropriate for production usage
  * *Mixed packages*; host both third-party and packages you create

We do not recommend using mixed packages, because there are a lot of options that won't be relevant for packages you create (like license filtering).


### Package File Storage & Retention {#package data-title="Package File Storage & Retention "}

By default, a feed's package files are stored on disk, in a directory managed by ProGet. You can change this directory by either editing the default directory, or by setting the Disk Path property on a feed.

:::attention {.analogy}
To use cloud storage, you need to configure a different package store; see [Amazon S3 and Azure Blob Package Storage](/docs/proget/advanced/cloud-storage) for more information.
:::

#### Retention Rules 

Retention rules help you reclaim disk space by automatically deleting old or unused packages that meet a set of criteria that you define. See [Retention Rules](/docs/proget/administration/retention-rules) for more information.

#### Package Storage 

Accessing the package files directly for any reason other than backing up files is not supported. Future versions of ProGet may change the way package files are stored on disk.

A feed's default directory is built by combining the following path elements:

{.docs}
- **Root Storage Path** - this path is configured in the `Storage.PackagesRootPath` setting (`Admin > Advanced Settings`), and defaults to `%ProGramData%\ProGet\PackagesRoot`. Its value may be assigned in Advanced Settings or, for load-balanced installations, may be set in the installation's .config file (web.config or ProGet.Service.config) with a key name of `ProGetConfig.Storage.PackagesRootPath`. To use the .config file value, the value specified here must be saved with an empty value. When configured in the .config file, that value will be displayed here.
- **Package Type Path** - each feed type uses a different setting and a different default. For example, npm feeds use `Storage.NpmPackagesLibrary`, which default to `.npm`.
- **FeedId.** The immutable, integer-based identifier for a feed

If the package type path is an absolute path, then the root storage path will be ignored. For example, assuming the default settings weren't changed, a NuGet feed with an ID of 1 would store packages in the following path:

```
%ProgramData%\ProGet\PackagesRoot\.nugetv2\F1
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


## Connectors & Replication {#connector data-title="Connectors & Replication"}

Connectors allow ProGet feeds to include packages from an external source, whether it is another ProGet feed, a public feed, or any other implementer of the package API for the feed type. See the [connectors documentation](/docs/proget/core-concepts/feeds/connectors) for more information about connectors.

[Feed replication](/docs/proget/advanced/feed-replication) allows you to replicate packages among different ProGet instances across the globe to allow different teams to share feeds without setting up complicated, global infrastructure. 

## Package Scanning & Blocking {#scanning data-title="Package Scanning & Blocking"}

You can configure your feeds in ProGet with a number of options for scanning packages for usage, vulnerabilities, and licenses -- and even blocking access to packages based on rules you configure.

* [Vulnerability Scanning and Blocking](/docs/proget/compliance/vulnerabilities) can automatically scan third-party packages and Docker container images against vulnerability databases

* [License Scanning and Blocking](/docs/proget/compliance/license-scanning) can help you managing licensing agreements of third-party, open-source packages. 

* [Package & Container Scanners](/docs/proget/advanced/package-container-scanners) can connect to an external resource such as Otter or Kubernetes to collect and display how packages and Docker container images are used across your infrastructure

## Webhooks & Variables{#webhooks data-title="Webhooks & Variables"}

[Webhooks](/docs/proget/advanced/webhooks) to allow you to integrate ProGet with other tools and  notify users, trigger automated workflows, or audit events like package creation and deletion.

## Feed API Endpoints {#api-endpoints data-title="Feed API Endpoints"}

All feeds have an "API Endpoint" URL that is used for API (programmatic) access. You can find this URL in the top-right corner of the Feed's browse page. The specific API that a feed uses depends entirely on its type; for example, a Universal feed will only use the [Universal Feed API](/docs/upack/feed-api), and a NuGet feed will only ever use the NuGet API.

## Deleting a Feed {#deleting data-title="Deleting a Feed"}

A feed can either be deactivated or deleted. Deactivating a feed will make it invisible from most of the user interface, and inaccessible from API endpoints.

Deleting a feed will remove it entirely from the software. All package data indexed in the database will be deleted, as well as metadata not stored in the package itself, such as publish dates and download counts for each package.

## Configuring Multiple Feeds {#multiple data-title="Configuring Multiple Feeds"}

The primary benefit of using multiple feeds is **package organization**. In theory, you could put everything into a single feed, but there are millions of packages out there, and maintaining them in one feed can becoming overwhelming very quickly. 

You can organize package feeds in a variety of ways. For instance, you may want to segment your feeds by the groups or teams that should be using them. This can help a team focus on only the packages they need without being burdened by packages that they don't. 

Another way to organize feeds is by Quality (Untested, Production-ready, etc). Packages move across development and testing pipelines much like any other software. ProGet is a great way to create different feeds for different stages of your pipeline. 

Configurations like security and storage can also be a determining factor on how to organize your various feeds. ProGet allows you to place [Security and Access Controls](/docs/proget/administration/security) to feeds and give teams different access to different feeds. For example, perhaps you'll only want Lead Engineers to be able to publish packages to the Production Feed.
