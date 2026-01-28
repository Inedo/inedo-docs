---
title: "Migrating ProGet Feeds Across Instances"
order: 5
---

It may be necessary to migrate all the packages you have stored in a ProGet feed from one instance to another, for example when setting up a new ProGet instance and needing to export all packages from your "old" instance of ProGet.

Depending on the version of ProGet you're using and the feed type, there are several ways to do this, and this document describes the different approaches:

* [Using Feed Replication](#using-feed-replication)
* [ProGet Feed-to-Feed Migration](#proget-feed-to-feed-migration)
* [Migrating Using a Bulk Upload](#migrating-using-a-bulk-upload)
* [Additional Migration Methods](#additional-migration-methods)

:::(warning) (⚠ Migration vs Backup & Restore)
These methods only migrate package content, not configuration data like download counts or custom licenses. This makes them a good fit for fresh installations with new feed names or settings. If you want to bring over everything, including metadata and configuation data, and can accommodate a brief maintenance window (typically about an hour), a full [backup and restore](/docs/installation/backing-up-restoring) is a better option.
:::

## Using Feed Replication
*This method requires at least two instances of ProGet with Enterprise Edition licenses. If you do not have ProGet Enterprise or your instances are isolated, you must use an alternative method.*

This is another recommended alternative for users on ProGet versions earlier than 2023.22, or users with a very large number of packages they need to migrate. 

Replication syncs two ProGet feeds together allowing you to create a feed on a new ProGet server instance and synch it with a currently existing one. This also means that any additions or changes to packages on the existing feed will be reflected in the new one.

To replicate a feed navigate to "Feeds" and select the Replication tab.

![Replication](/resources/docs/proget-feed-replication.png){height="" width="50%"}

See our page on [Feed Replication](/docs/proget/replication-feed-mirroring/proget-advanced-feed-replication) to learn more.

## ProGet Feed-to-Feed Migration
*This method requires ProGet 2025.0+ and works with all feed types. Docker feed-to-feed migration requires ProGet 2025.13+*

The built-in Feed-to-Feed is the preferred method for migration and involves using [ProGet's step-by-step instructions](/docs/proget/feeds/feed-overview/proget-feed-importing) to migrate all packages from an existing feed to a newly created one.

It is easy to configure and involves creating a new feed in an instance that migrates across all packages from a feed in another instance.

After creating a new feed, you will have the option to import packages from an existing feed on another ProGet server. Select "Import Packages" from the dropdown menu.

![Import](/resources/docs/proget-import-packages-select.png){height="" width="50%"}

From here select "Download Package from Another Service" and then "ProGet".

![Import](/resources/docs/proget-import-packages-select-proget.png){height="" width="50%"}

From here you will need to enter your **ProGet server URL** of your previous instance, as well as an **API Key**, so make sure you have these on hand when setting up your new feed. You'll then be asked to select which feed from your previous instance you want to import packages from. 

:::(warn)(⚠ Docker Image Importing)
When importing Docker images from from another ProGet instance < 2025.13, you must remove all connectors from your Docker feed prior to importing.
:::

## Migrating Using a Bulk Upload
*This method requires ProGet 2023.22+ and works with all feed types except Docker and Bower. For Maven feeds, this method requires ProGet 2023.22+.*

Bulk Upload method is a good alternative for users on ProGet versions earlier than 2023.22.

Using bulk upload is simple but requires packages to be manually copied to the target server, or to a network path accessible from the target server. 

:::(Info)
Before migrating via drop path we recommend clearing your [Feed Connector cache](/docs/proget/feeds/connector-overview#connector-caching?#connector-caching). Otherwise, you'll import all of your cached packages.
:::

See the [Uploading via Bulk Upload](/docs/proget/installation/migrating-to-proget/proget-other-feed-migration#uploading-via-bulk-upload) section of our guide on [Migrating From Other Repositories to ProGet](/docs/proget/installation/migrating-to-proget/proget-other-feed-migration) to learn more.

## Additional Migration Methods

Users of older versions of ProGet may not be able to use the methods listed above as certain features or package types may not have been implemented. For these users, there are a few options available:

### Connector Downloaders (Legacy)

This involves setting up a Feed Connector to directly connect to a ProGet feed, and then importing packages from it. The feature will likely be removed in ProGet 2024, in favor of Feed-to-Feed migrations.

See the [Configuring a Connector](/docs/proget/installation/migrating-to-proget/proget-other-feed-migration#configuring-a-connector) section of our guide on [Migrating from other repositories](/docs/proget/installation/migrating-to-proget/proget-other-feed-migration) to learn more on how to set this up.

By selecting "Manage Feed" in any feed and navigating to "Connectors", you can then import your packages by clicking the "Import Icon" to the left of the delete icon.

![Import](/resources/docs/proget-connector-import.png){height="" width="50%"}

The icon is only available for supported feed types and non-public repositories. It is intended for migration and is not supported for public open-source repositories (e.g. nuget.org, npmjs.org), which have an extremely large number of packages.

### Direct Database and File System Import/Export (Not recommended / Not Supported)

This method is complicated, and the samples provided only work on ProGet 2022 and earlier. We don't recommend using this method... but if you're comfortable with SQL Server it might work for you.

#### Step 1: Find Feed ID and Storage Path

Determine the feed ID of the source feed by navigating to it's "Manage Feed" page and looking at the "?feedId=" part of the URL in the browser. Then locate the package storage path from the source feed.

#### Step 2: Execute Export Script

Connect to the source instance's database using SSMS (for SQL Server). Next, execute an [export script](https://gist.github.com/inedo-builds/3bfdf846fb78fdf5ecd03d736682679f) against your ProGet database, substituting the feed ID determined in Step 1.

#### Step 3: Create a Feed

Save the XML output from Step 3 into a file, and copy the file to the server hosting the target's database. Then, create a new feed of the same type in the target instance.

#### Step 4: Copy Contents

Locate the package storage path of the target feed by navigating to it's "Manage Feed" page and copy all contents recursively from the path determined in Step 1 to this path.

#### Step 5: Execute Import Script

Connect to the target instance's database using SSMS and execute the [import script](https://gist.github.com/inedo-builds/80486de84fd413313d54eb80edb821bf), substituting the target feed ID and inputting the XML file name. Finally, disable the old feed.
