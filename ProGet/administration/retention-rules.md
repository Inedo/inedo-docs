---
title: Retention Rules
sequence: 100
keywords: proget,packages
---

This feature is available in paid and trial ProGet editions. {.info}

Retention rules help you reclaim disk space by automatically deleting old or unused packages that meet a set of criteria that you define.

A ProGet Basic license is currently required to use feed retention. Although rules can still be configured in the free edition, they are always executed in [dry run mode](#dry-run-mode) in ProGet Free.

## Creating Retention Rules {#creating data-title="Creating Retention Rules"}

  Retention rules are configured on a feed-by-feed basis by going to the Manage Feed page, and describing the packages you want to delete.

A rule is comprised of the following options:

| Action | Result |
| ------ | ------ |
| Delete Cached connector Packages | Packages that have been cached from a connector will be considered for deletion |
| Delete prerelease versions | Packages with a "prerelease" versions (such as 4.1.0-beta) will be deleted |
| Delete old versions | All except the latest N versions of a package will be deleted |
| Delete unused versions | All packages except those that have been downloaded in the last N days and with less than Y downloads will be deleted |
| Delete/keep by name | Packages with the specified names will either be deleted or not deleted |

When multiple options are specified, then only packages that meet all of the selected criteria are considered. See matching examples for more information.

## Quotas {#Quotas data-title="Quotas"}

A rule can be defined to only run when a certain size threshold is reached.

There are three possible quota configurations for a rule:

| Configuration | Result |
| ------------- | ------ |
| none | No quota is defined; the rule will always delete every package that matches the rule's filters. |
| size of matching packages | The user-specified size applies to the total size of all packages that match the rule's filters. The rule will only delete packages until the remaining matches consume disk space less than or equal to the specified size. |
| size of entire feed |   The user-specified size applies to the total size of *all* packages in the feed; even packages that do not match the rule's filters. The rule will still only delete matching packages, but it will do so until the size of the total feed is less than or equal to the specified size. |

When a quota is defined, ProGet will only delete matching packages until the specified size is met.

In determining which packages to delete first, it will use the following priority:

1. Cached packages are always deleted first.
2. Prerelease packages are considered if there are no more matching cached packages.
3. Previous versions of matching packages are considered if there are no more cached or prerelease matches.
4. All other matches are considered in no guaranteed order.

This delete order is implemented as a convenience and should **not** be relied upon to keep packages from being deleted. If you want to ensure a package is safe from retention, **make sure that it will never be matched by a rule in the first place**.

## Dry Run Mode {#dry-run-mode data-title="Dry Run Mode"}

On the Admin > Advanced Settings page is a **FeedRetentionDryRun** option. Setting this option to **True** will prevent any retention rules in ProGet from actually deleting anything. It will still run the rules as normal and log the packages that *would* be deleted if dry run mode was not enabled, but will skip the actual deletion.

This can be useful to evaluate whether a rule is correct before running it on a live feed.

Testing and dry run mode are **not** intended to be a substitute for a proper backup plan for your package repository. Packages deleted through retention are deleted **permanently**. Production environments should perform regular repository backups in case of any accidents.

## Retention Rule Matching Examples {#matching data-title="Retention Rule Matching Examples"}

ProGet's retention rules are powerful enough to handle many use cases. Following is a list of some common scenarios and how to configure them.

### Delete Cached Packages not Downloaded in 30 Days

This will delete all packages cached from connectors that nobody has requested within 30 days of the time the rule is evaluated.

{.docs}
- Check **Delete cached connector packages**
- Check **Delete unused versions** and supply **30** days

### Delete Previous Prerelease Versions

This will delete all prerelease versions of all packages,
*except* for the *most recent* prerelease version.

{.docs}
- Check **Delete prerelease versions**
- Check **Delete old versions** and supply latest **1** version

### Delete Old Versions of Specific Packages

This will delete all except the latest 5 versions of any packages matching the wildcard filter **my.package**.\*

{.docs}
- Check **Delete old versions** and supply latest **5** version
- Check **ilter by package name** and supply **my.package**.\* as the **delete packages with matching IDs** value.
