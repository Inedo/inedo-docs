---
title: "What is an Asset Directory?"
order: 1
---

ProGet provides a robust infrastructure for hosting and distributing your application and component packages, and often it can be useful to distribute assets associated with those packages (such as certificate files, third-party installers, license key files, and so on) in ProGet as well.

You could package these files, but they are not versioned by nature, do not require additional metadata, and packing and unpacking them would cause additional and unnecessary steps. Sometimes a file is just a file, and ProGet asset directories provide the versatility of a file share with the added benefit of security and stability features such as [scoped privileges](/docs/proget/administration-security), retention policies, automatic failover, and feed replication. Setting up an asset directory in ProGet and [migrating from a network share](/docs/proget/asset-directories-file-storage/proget-howto-migrating-from-a-network-share-to-hosted-files) is straightforward. 

:::(INFO)
Example: An installer can be used by a configuration script to set up a workstation. The installer is already a self-contained package, so further packaging would be unnecessary; it just needs to be downloaded and run. It could simply be stored in an asset directory and referenced by URL.
::: 

## Asset Directory

An asset directory is like a [feed](/docs/proget/feeds/feed-overview), except that it contains arbitrary files instead of package files. You can access these files through your web browser and the RESTful API.

![proget-asset-item](/resources/docs/proget-asset-item.png){height="" width="50%"}

Internally, asset directories are represented as feeds, which means:

- feed-scoped privileges can be applied on asset directories
- you can configure [cloud package storage](/docs/proget/advanced-features/proget-advanced-cloud-storage) for [Amazon S3](/docs/proget/advanced-features/proget-howto-amazon-s3-ui) and [Azure Blob storage](/docs/proget/advanced-features/proget-howto-azure-blob-ui)
- you can use [feed replication](/docs/proget/replication-feed-mirroring/proget-advanced-feed-replication) just like regular feeds
- [retention policies](/docs/proget/administration/retention-rules) work just like regular feeds, except version number-based policies do not apply
- the asset directory name cannot be the same as another feed name

:::(INFO)
Note: [Connectors](/docs/proget/feeds/connector-overview) are not currently supported for asset directories, but may be added in a future release. Please [contact us](https://inedo.com/contact) if you are interested in these additions.
:::

To create an asset directory, go to Assets > Create Asset Directory.

## Files in an Asset Directory

Asset Directories are made up of files and [sub-folders](#subfolders) that you can access through your web browser and the RESTful API. You can view file information, caching, metadata, and download information by clicking on a file in ProGet Web UI. Files can also be accessed as if they were hosted on a web server by navigating to the file's URL (see [Asset Directory API](/docs/proget/api/assets)). This URL is also displayed on the file information page in the Web UI.

## Adding Files

Files can be added to an asset directory in a number of ways. You can:

#### Add/Upload Files

These options can be found by clicking on the _Add/Upload Files_ button in the upper right corner.

- Upload files; upload a single file or multiple files through your browser.
- Upload via API: Use the asset directory API to upload one or more files to the asset directory.
- Import from a zip file: Upload a .zip or .tgz file using your browser and expand its contents into the asset directory.
- Import from Amazon AWS; download files stored in the cloud to this asset directory (**Requires ProGet v6.0+ and the Amazon AWS Extension**).
- Import from Azure; Download files stored in the cloud to this asset directory (**Requires ProGet v6.0+ and the Azure Extension**).

:::(Info) 
Read how to use ProGet as a User-Interface for [Amazon S3](/docs/proget/advanced-features/proget-howto-amazon-s3-ui) and [Azure Blog Storage](/docs/proget/advanced-features/proget-howto-azure-blob-ui) in our step-by-step guides.
:::

#### Import from a Drop Path

Starting with ProGet 6.0, you can configure an asset directory to use a "drop path", which is a local or network path that ProGet periodically scans for files. If this directory contains a file, it is imported into this asset directory.

To configure the _Drop Path_:
1. Navigate to the asset directory
1. Click _Manage Directory_ in the multi-button in the upper right corner 
1. Select the _Storage & Retention_ tab
1. Click configure to the right of _Drop Path_
1. Enter the local or network path to be monitored

:::(INFO)
Note: If you are using a network path, you must configure read, write, and delete access for the account under which the ProGet Windows service is running.
:::

### Deleting Files 

You can delete a file in the asset directory by clicking the "X" on the file listings page, the "Delete File" button on the file overview page, or by using the [Asset Directory API](/docs/proget/api/assets).

### Media Types

When you upload a file to an asset directory, ProGet attempts to determine the file's [media type](https://en.wikipedia.org/wiki/Media_type). The media type is sent as a content-type response header when the file is downloaded, which may be used by the downloading application or script.  For example, if the content-type is `application/msword`, then a web browser may open Microsoft Word to display the file. 

You can change the file's media type by:
1. Navigate to the file in the web UI
1. Click _set_ to the right of _Type_
1. Enter the [content type](https://en.wikipedia.org/wiki/Media_type) you would like to return

You can also set the content-type by using the [Asset Directory API](/docs/proget/api/assets).

## Sub-folders

Similar to a file system, you can use sub-folders to organize the files in an asset directory. Sub-folders may also contain other sub-folders.

You can view or edit sub-folder properties by clicking the pencil to the right of the sub-folder in the list, or by navigating to the folder and clicking the _Folder Properties_ button in the multi-button in the upper right corner.

### Adding Sub-folders

You can add a sub-folder by navigating to that asset directory or a sub-folder, and then clicking Create Sub-folder" in the multiple button in the upper-right corner, or by using the Asset Directory API.

### Deleting Sub-folders

You can delete a sub-folder by clicking the "X" on the file lists page, clicking the "Delete Folder" button on the folder properties page, or using the Asset Directory API.

## Custom Metadata 
Custom metadata is available as of ProGet v6.0.*

Additional metadata can be stored in ProGet for files and sub-folders. Examples of metadata can include creator information, internal notes, build information, release information, and more. Files can also contain the metadata record as an HTTP header included in the HTTP download response.

### File Metadata

You can add metadata to files by:
1. Navigating to the file
1. Clicking _Add_ under the _Additional Metadata_ section
1. Entering the name and value of the metadata record
1. Optionally, you can select _Include as HTTP response header_ to send that record as an HTTP header value

Metadata can also be managed using the [Asset Directory Metadata API](/docs/proget/api/assets/metadata-endpoints/proget-api-assets-metadata-set).

### Sub-folder Metadata

You can add metadata to a sub-folder by:
1. Navigating to the folder's properties
    1. Clicking the pencil to the right of the sub-folder in the list 
    1. Navigating to the folder and clicking the _Folder Properties_ button in the multi-button in the upper right corner
1. Clicking _Add_ under the _Additional Metadata_ section
1. Entering the name and value of the metadata record

Metadata can also be managed using the [Asset Directory Metadata API](/docs/proget/api/assets/metadata-endpoints/proget-api-assets-metadata-set).

## Client-side Caching 
*Client-side Caching is available starting in ProGet v6.0.*

ProGet can instruct a user's browser or client tool to cache asset files in their local storage, reducing the number of requests to your ProGet server. Custom client-side caching can be set at the file or sub-folder level, which then returns the correct cache headers with the HTTP download request. When a cache value is set for a sub-folder, all files and sub-folders contained in that folder inherit that cache setting by default. You can override this setting for any child file or sub-folder. For example, you can set a folder to cache files for 30 minutes, but override a specific file in that folder so that it is not cached at all, while the remaining files use the inherited 30-minute cache setting.

To configure caching:
1. Navigate to a sub-folder's properties or to a file in the Web UI.
2. Click _set_ to the right of _Cache Header_
3. Select and configure the cache type:
    1. (inherited); inherit the cache header from its parent sub-folder
    1. Do Not Cache; do not cache the file in its local cace
    1. Browser Cache TTL; cache the file for a specific amount of time in its local cache
        1. Short (30 minutes)
        2. Medium (2 weeks)
        3. Long (1 year)
        1. Custom; duration in seconds
    1. Custom Cache-Control Header; specify a custom [Cache-Control](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control) header to be returned in with the download request
1. Click _Save_

:::(INFO)
Client-side cache settings are client-side only, they will _not_ enable/disable any server-side caching.
:::

