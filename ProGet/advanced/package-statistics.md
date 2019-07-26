---
title: Package Statistics
subtitle: Package Statistics
sequence: 300
keywords: BuildMaster, manual
---

ProGet tracks the use of packages in your feeds to help you decide which packages are being used, which packages are taking a lot of disk space, and which packages are frequently cached.

## All Feed Disk Usage  {#all-feed data-title="All Feed Disk Usage"}

On the ProGet Overview page, you can see how much total disk space each feed is using.

![](/resources/documentation/proget/feed-disk.png){.screenshot}

## Feed-specific Disk Usage Statistics  {#feed-specific data-title="Feed-specific Disk Usage Statistics"}

On the Manage Feed page, you can identify if any packages are consuming a disproportionate amount of disk space, as well as how much cached packages (i.e. packages downloading from connectors) are using.


![](/resources/documentation/proget/package-usage.png){.screenshot}

This can help you when you define [retention rules](/docs/proget/administration/retention-rules) to cut back on disk space usage if needed.


## Package Statistics  {#package-statistics data-title="Package Statistics"}

ProGet can also be configured to track individual package downloads and record every user that downloads every package.

This feature is available in paid and trial ProGet editions. {.info}

Because this can potentially consume a lot of database disk space, you must first enable it by going to Administration > Advanced Settings and checking the Package Statistics enabled box. Once enabled, ProGet will record the following information each time a package file is requested:

{.docs}
- **Username** – the name of the authenticated user who downloaded the package, or “Anonymous” if the user was not logged in
- **IP Address** - the IP address that requested the package
- **User Agent** - the user agent header that was sent in the download request
- **Date/time** - the specific date/time the package was downloaded

### Viewing in the UI  {#viewing-ui data-title="Viewing in the UI"}

On the Package page, you can see an aggregate of download data by clicking on the Package Statistics button on a package overview page.

![](/resources/documentation/proget/package-statistics.png){.screenshot}

### Advanced Reporting  {#advanced-reporting data-title="Advanced Reporting"}

For more advanced analysis of package downloads, you can query the `ProGet SQL` Server database directly. Each download record is stored in the `PackageDownloads` table.
