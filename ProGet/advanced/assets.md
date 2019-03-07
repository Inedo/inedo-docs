---
title: Asset Directories And File Storage
subtitle: Asset Directories And File Storage
sequence: 400
keywords: proget,assets
---

ProGet provides a robust infrastructure for hosting and distributing your application and component packages, and oftentimes it can be convenient to distribute assets related to those packages – certificate files, third-party installers, license key files, etc. - in ProGet as well.

While you could certainly package these files, they are inherently un-versioned, require no additional metadata, and packaging and unpackaging would create extra and unnecessary steps. Sometimes, a file is just a file, and ProGet asset directories provide the versatility of a file share, with the added benefit of security and stability features such as [scoped privileges](/support/documentation/proget/administration/security), retention policies, and automatic failover.

:::attention {.analogy}
Example: An installer may be used by a configuration script for setting up a workstation. The installer is already a self-contained package, so packaging it further would be redundant; it just needs to be downloaded and executed. It could simply be stored in an Asset Directory, and referenced by URL.
:::

## Asset Directory {#asset data-title="Asset Directory"}

An asset directory is a like a [feed](/support/documentation/proget/core-concepts/feeds), except for arbitrary files instead of package files. You can access these files from your web browser and the RESTful API.

Internally, asset directories are represented as feeds, which means:

{.docs}
- feed-scoped privileges can be applied on asset directories
- you can configure Amazon S3 and Azure blob storage
- [retention policies](/support/documentation/proget/administration/retention-rules) work just like regular feeds, except version number-based policies do not apply
- the asset directory name cannot be the same as another feed name

:::attention {.analogy}
Note: [Connectors](/support/documentation/proget/core-concepts/feeds/connectors) and [feed replication](/support/documentation/proget/advanced/feed-replication) are not currently supported for asset directories, but may be added in a future release. Please [contact us](/contact) if you are interested in these additions.
:::

To create an asset directory, go to Assets > Create Asset Directory.

## Files in an Asset Directory {#files data-title="Files in an Asset Directory"}

Files can be added to an asset directory in a number of ways. You can:

{.docs}
- Upload File; upload a single file using your browser.
-  Import Archive; upload a .zip or .tgz file using your browser and expand its contents into the asset directory
- Upload using API; use the Asset Directory API to upload one or more files to the asset directory.
- Upload Large File or Multiple Files; upload a large file (1GB+) or multiple files at once. This is only supported for asset directories that use the directory package storage mechanism.

Files in an asset directory can also be accessed as if they are hosted on a web server by navigating to the file's URL (see [asset directory API](/support/documentation/proget/reference/asset-directories-api)). This URL is also displayed on the file information page in the Web UI.

### Deleting Files {#deleting data-title="Deleting Files"}

You can delete a file in asset directory by clicking the "X" on the file listings page, the "Delete File" button on the file overview page, or by using the [asset directory API](/support/documentation/proget/reference/asset-directories-api).

### Media Types {#media data-title="Media Types"}

When you upload a file to an asset directory, ProGet will attempt to determine the file's [media type](https://en.wikipedia.org/wiki/Media_type). The media type is sent as a content-type response header when the file is downloaded, which may be used by the downloading application or script.

:::attention {.analogy}
For example, if the content-type is application/msword, then a web browser may open Microsoft Word to display the file. You can change the file's media type by navigating to the file in the web UI, and clicking the *set* option.
:::

### Subdirectories {#subdirectories data-title="Subdirectories"}

Similar to a file system, you can use subdirectories to organize the files in an asset directory. Subdirectories may also contain other subdirectories.

You can delete a subdirectory by clicking the "X" on the file listings page or by using the asset directory API.
