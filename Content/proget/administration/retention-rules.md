---
title: "Retention Rules"
order: 1
---

This feature is available in paid and trial ProGet editions. 

Retention rules help you reclaim disk space by automatically deleting old or unused packages that meet a set of criteria that you define, while allowing you to identify specific packages to exclude from these rules. If you're coming from Artifactory and want additional guidance, see [How Retention and Data Storage Work in ProGet for Artifactory Users](https://blog.inedo.com/proget-migration/how-retention-and-data-storage-work-in-proget-for-artifactory-users/) on the Inedo blog.

A ProGet Basic or Enterprise license is currently required to use feed retention. Although rules can still be configured in the free edition, they are always executed in [dry run mode](/docs/proget/administration/retention-rules#dry-run-mode) in ProGet Free.

## Creating Retention Rules

  Retention rules are configured on a feed-by-feed basis by going to the Manage Feed page and navigating to the Storage & retention tab, and describing the packages you want to delete.

A rule is comprised of the following options, based on the feed type. When a rule has multiple options selected, only packages that meet ALL of the selected criteria are evaluated for action. See [matching examples](#matching) for more information.

A feed can have multiple rules applied, in this case rules are run in the order they appear in the ProGet UI (top to bottom).If [quotas](#Quotas) are being used it is possible not all rules will run after quota conditions are met. 

### Package rules

| Action | Result |
| ------ | ------ |
| Delete cached connector packages | Packages that have been cached from a connector will be considered for deletion |
| Delete prerelease versions | Packages with a "prerelease" versions (such as 4.1.0-beta) will be deleted |
| Delete old versions | All except the latest N versions of a package will be deleted |
| Delete unused versions | All packages except those that have been downloaded in the last N days and with less than Y downloads will be deleted |
| Delete/keep by package name | Packages with the specified names will either be deleted or not deleted; this field supports [wildcards](#wildcards) |
| Delete/keep by package version | Packages with the specified versions will either be deleted or not deleted; this field supports [wildcards](#wildcards) |
|  Delete/keep consumed packages | Packages consumed by other packages can be deleted if not used in other feeds or not deleted if consumed by packages in other feeds |

### Container rules

| Action | Result |
| ------ | ------ |
| Delete cached connector packages | Images that have been cached from a connector will be considered for deletion |
| Delete untagged manifests | Manifests that do not have an associated tag will be considered for deletion |
| Delete unused versions | All images except those that have been downloaded in the last N days and with less than Y downloads will be deleted |
| Delete/keep by repository name | Images with the specified names will either be deleted or not deleted; this field supports [wildcards](#wildcards) |
| Delete/keep by image tag | Images with the specified tags will either be deleted or not deleted; this field supports [wildcards](#wildcards) |
| Delete when all package usage removed | Images are removed when the [container has not been used](/docs/proget/docker/container-scanners) for N days |

*Untagged and tagged images cannot be deleted by the same retention rule.*

### Asset rules

| Action | Result |
| ------ | ------ |
| Delete unused files | All files except those that have been downloaded in the last N days and with less than Y downloads will be deleted |
| Delete/keep by file name | Files with the specified names will either be deleted or not deleted; this field supports [wildcards](#wildcards) |

### Wildcards

Certain fields noted above support wildcard syntax. An asterisk (`*`) may be used when specifying names or versions, and it will match 0 or more characters.

For example, if "keep packages with matching IDs" is configured as: 

```
Microsoft.*
Castle.*
```

the retention rule will keep all packages whose IDs start with `Microsoft.` or `Castle.`.

## Quotas 

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

## Dry Run Mode 

On the Admin > Advanced Settings page is a **FeedRetentionDryRun** option. Selecting this option will prevent any retention rules in ProGet from actually deleting anything. It will still run the rules as normal and log the packages that *would* be deleted if dry run mode was not enabled, but will skip the actual deletion.

This can be useful to evaluate whether a rule is correct before running it on a live feed. To trigger a dry run after setting the **FeedRetentionDryRun** option go to the admin section and select "Scheduled Jobs", you will see a list of scheduled tasks, with a green play button next to them. Select the retention rules you want to run and click the play button and you will see logs of which packages would have been affected by the rule.

Testing and dry run mode are **not** intended to be a substitute for a proper backup plan for your package repository. Packages deleted through retention are deleted **permanently**. Production environments should perform regular repository backups in case of any accidents.

## Retention Rule Matching Examples

ProGet's retention rules are powerful enough to handle many use cases. Following is a list of some common scenarios and how to configure them.

### Delete Cached Packages Not Downloaded in 30 Days

This will delete all packages cached from connectors that nobody has requested within 30 days of the time the rule is evaluated.

- ☑ **Delete cached connector packages**
- ☑ **Delete unused versions** and supply **30** days

### Delete Previous Prerelease Versions

This will delete all prerelease versions of all packages,
*except* for the *most recent* prerelease version.

- ☑ **Delete prerelease versions**
- ☑ **Delete old versions** and supply latest **1** version

### Delete Old Versions of Specific Packages

This will delete all except the latest 5 versions of any packages matching the wildcard filter **my.package**.\*

- ☑ **Delete old versions** and supply latest **5** version
- ☑ **Delete/keep by package name** and supply **my.package**.\* as the **delete packages with matching IDs** value

### Delete Unused CI Package Versions

This will delete all package versions that have "CI" in its prerelease version component that haven't been downloaded for 2 days:

- ☑ **Delete prerelease versions**
- ☑ **Delete unused versions** and supply **2** days
- ☑ **Delete/keep by package version** and supply `*-CI.*` as the **delete packages with matching versions** value

*Note: while checking the option to "delete prerelease versions" and supplying a wildcard pattern that only matches prerelease versions is redundant, it is a best practice to be as explicit as possible in the policy.*

## API

Retention rules may be configured via the [Feed API](/docs/proget/api/feeds). See the [Retention Rule data model specification](/docs/proget/api/feeds#retention-rule-object) for more information.