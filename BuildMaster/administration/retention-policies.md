---
title: Retention Policies
subtitle: Retention Policies
keywords: buildmaster, retention-policy, cleanup
sequence: 900
---

BuildMaster retains a *lot* of logs; this is great when you need to debug what went wrong, audit deployments, and so on -- but it's not so great when you start running out of disk space. This is where *retention policies* come in.

Retention policies may be created that will allow the system to reclaim disk space and/or database space depending on the configured policy. Policies may be filtered by application, application group, or pipeline, and may also specify a time window that can be used to prevent the retention policy from executing its purge methods during normal operational hours.

### What Can Be Purged {#what-purge data-title="What Can Be Purged"}

There are 4 possible objects that can be purged by configuring a retention policy, and each may be filterable additionally by release status, build status, and/or environment:

{.docs}
-   **Releases** - purges all filtered release data including promotions, builds, executions, and logs
-   **Builds** - purges all filtered build data including promotions, executions, and logs
-   **Execution Logs** - purges all execution logs associated with specific environments
-   **Artifacts** - purges artifact files from disk

**Note:** purging either Releases and Builds **will not** purge artifacts from disk for those builds. To clean up the artifact zip files from disk, a second retention policy must be added with the option to "purge orphaned artifacts" checked. {.info}

### Retention Filtering & Options {#retention-filtering-options data-title="Retention Filtering & Options"}

Retention policies will only perform purges if the criteria specified in a particular policy is met. There are 7 main filters that apply to most purge objects:

{.docs}
-   **Application**
-   **Application Group**
-   **Workflow**
-   **Environment**  *(applies to purging logs only)*
-   **Time of Day**
-   **Release Status**
-   **Build Status**  *(does not apply to purging releases)*

Beyond these standard filter options, you may also configure additional retention filtering that prevents certain items from being purged regardless if they match the filters or not:

{.docs}
-   **Count** - keep the last N items
-   **Days** - only purge items if they are older than N days
-   **Size** - keep at least the last N MB worth of artifacts/logs

Additionally, there is an option for the purge **Build Artifacts** type that will also purge artifacts from disk no longer have a reference to them within BuildMaster. This setting will ignore any filters that may be applied and always delete all orphaned artifacts.

### Immediate Purge {#immediate-purge data-title="Immediate Purge"}

Retention policies may also be forced to run immediately, bypassing any day/time window restrictions.
