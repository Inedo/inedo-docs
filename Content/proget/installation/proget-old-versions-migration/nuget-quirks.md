---
title: "NuGet (Quirk) Feeds"
---

:::(error) (NuGet (Quirk) Feeds Were Discontinued in ProGet 5.3)
This article is for users who are using NuGet (Quirk) Feeds in ProGet 5.2 and earlier.
:::

NuGet feeds created before ProGet 5.0 are considered legacy NuGet (Quirks) feeds. The Quirks feed type uses the loose semantic versioning rules that NuGet.org has historically favored. In ProGet 5.0, new NuGet feeds now use a more sophisticated versioning scheme that takes into account the Semantic Versioning 2.0 rules for pre-release version strings. However, versions that violate the SemVer2 specification will still be displayed in SemVer2 feeds.

## Migrating NuGet (Quirks) Feed
To migrate a legacy NuGet (Quirks) feed, click the Migrate link on the Manage Feed page. ProGet opens a confirmation dialog with a set of migration options. When performing a migration, ProGet performs the following steps:

- Set the feed to `disabled` state
- Add new metadata (Database) entries to the feed
- Remove old (Quirks) metadata for the feed
- Reactivate the feed

This migration is strictly a database operation that does not require your packages to be changed at all. By default, the migration is performed as a single database transaction, so it can be undone in the unlikely event of an error. If you have a very large number of packages in your feed (> 10,000), we strongly recommend that you back up the ProGet SQL database before performing the migration and disable this single transaction mode by clearing the corresponding check box in the Migrate Feed dialog.

Legacy (Quirks) NuGet Feeds were removed in ProGet 5.3, and upgrading will be blocked unless you've migrated or deleted all of these feeds.

## Legacy NuGet API Key 

NuGet (Quirks) feeds supported an API key definition directly on the Manage Feed page; these keys will be removed when upgrading to ProGet 5.3.

Legacy NuGet API keys behave slightly differently than ProGet API keys: if a legacy key is defined for the feed, it is checked *in addition* to the authenticated user. If the same key is defined as both a legacy key and a ProGet key, the ProGet key takes precedence.
