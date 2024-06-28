---
title: "Asset Versioning & Automatic Retention Rules"
order: 2
---

Starting in ProGet v6.0.0, asset directories support versioning.

## Versioning Overview
When versioning is enabled for an asset directory, the previous contents of a file are always preserved when it is overwritten or deleted. Note that **only** the contents of the old versions are saved, *not* the associated metadata. This allows you to undo an accidental change or deletion and download an old version of a file.

Versioning is supported by any ProGet storage system (disk or cloud) and works by storing copies of files in a special directory, `._proget_file_history_`, as soon as they are overwritten or deleted.

### Limitations
Asset content is archived, not asset metadata. Previous versions of assets are not replicated.

## Versioning Best Practices
Asset directory versioning is intended to help prevent accidental destructive changes; it is not intended as a substitute for a differencing/change tracking system such as source/version control. When used with very large files, the asset directory could consume disk space if the files are changed frequently.

As with any tool that stores previous versions, be careful not to store sensitive information in assets.

## Retention Rules for Asset Directories
Asset directories support retention rules just like package feeds in ProGet. The most common use case for asset retention rules is to delete files that are accessed infrequently (e.g., when an asset directory is used as an on-premise cache for offsite resources).

However, at this time, deleting old versions of assets (when versioning is enabled) is not supported. We may consider adding this in the future; please contribute to [the open discussion](https://forums.inedo.com/post/15723) if this would be of interest to you.

Read more about retention rules at [Retention Rules](/docs/proget/administration/retention-rules).