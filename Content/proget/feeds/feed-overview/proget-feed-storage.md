---
title: "File Storage & Drop Paths"
order: 3
---

By default, a feed's package files are stored on disk in a directory managed by ProGet. You can change this directory by either editing the default directory or setting the Disk Path property for a feed.

:::(Info)
To use [Amazon S3 and Azure Blob Package Storage](/docs/proget/advanced-features/proget-advanced-cloud-storage) for cloud storage, you need to configure a different package store. This feature is not available in free versions of ProGet.
:::

## Package Storage 

:::(Warning)
Direct access to package files for purposes other than backing up files is not supported. Future versions of ProGet may change the way package files are stored on disk.
:::

The default directory of a feed is created by combining the following path elements:

- **Root Storage Path** - This path is configured in the `Storage.PackagesRootPath` setting (`Admin > Advanced Settings`), and defaults to `%ProGramData%\ProGet\PackagesRoot`. Its value may be assigned in Advanced Settings or, for load-balanced installations, set in the installation's .config file (web.config or ProGet.Service.config) with a key name of `ProGetConfig.Storage.PackagesRootPath`. To use the .config file value, the value specified here must be saved with an empty value. If it is configured in the .config file, this value is displayed here.
- **Package Type Path** - Each feed type uses a different setting and a different default. For example, npm feeds use `Storage.NpmPackagesLibrary`, which defaults to `.npm`. In general, you should keep the default settings, but it may be useful to change them if you want to enable data deduplication for all feeds of a certain type.
- **FeedId.** The immutable, integer-based identifier for a feed.

If the package type path is an absolute path, the root storage path is ignored. For example, assuming that the default settings haven't been changed, a NuGet feed with an ID of 1 would store packages in the following path:

```bash
%ProgramData%\ProGet\PackagesRoot\.nugetv2\F1
```

###  Automatic Retention/Deletion of Packages
Retention rules, which can be configured at the feed level for each feed, help you reclaim disk space by automatically deleting old or unused packages that meet a set of criteria you define. See [Retention Rules](/docs/proget/administration/retention-rules) for more information.


### Changing Directories 

:::(Warning) 
If you change the disk directory of a feed, either directly (by going to the feed settings page) or indirectly (by editing the path defaults), the package files are not moved.
:::

If you want to change the directories where your packages are stored, you must also move/copy the contents from the current location to the new location. We generally recommend:

1. Plan for downtime and inform your users.
2. Change the desired settings in ProGet 
3. Disable the feed or the ProGet application completely 
4. Transfer the files to the new location 
5. Activate ProGet again

You can also leave ProGet online all the time; this just results in a series of error messages like "Package file not found" if someone tries to download the package before the transfer is complete.

Depending on how many package files you have, the transfer may take a considerable amount of time; you may not want ProGet to be offline or users to receive errors during the process. In this case, we recommend that you first mirror the files a few times using a tool like `robocopy /MIR` (in case packages were uploaded during the first copy) and then change the settings in ProGet.

#### Storage Access & Permissions 

Both the ProGet web application (either the login user for the `INEDOPROGETWEBSVC` service for the Integrated Web Server, or the associated application pool identity of the ProGet IIS site) and the login user for the `INEDOPROGETSVC` service must have full control rights over the disk directory of a feed.

If you are using network storage, you should use the path UNC (for example, `\\\filesv1\packages`) rather than the mapped drive (`P:\packages`). The reason for this is the way mapped drives work in Windows; they can be somewhat difficult to configure and remain functional for service accounts.

## Drop Path and Bulk Package Importing 

In some cases, it may be easier for users or programs to simply copy package files to a network drive rather than using the API of this feed type to publish them to ProGet. A common scenario for this is a bulk import of packages.

[You can configure a feed to use a "drop path,"](/docs/proget/feeds/feed-overview/proget-bulk-import-with-droppath) which is a local or network path that ProGet will scan for files periodically. If that directory contains a package with a valid format for the feed type, it will be imported to ProGet.

By default, existing packages will not be overwritten, but you can change this by going to "Administration Overview" > "Advanced Settings", and changing `Feeds.AllowDropPathImportOverwrite` to `true`.