---
title: "Retention Policies"
order: 6
---

BuildMaster stores a lot of logs; that's great when you need to troubleshoot, review deployments, and so on—but it's not so great when you begin to run out of storage. This is where retention policies come in.

Retention policies can be created that allow the system to reclaim disk and/or database space, depending on the policy configuration. Policies can be filtered by applications, application groups or pipelines. In addition, a time window can be set to prevent the retention policy from executing its purge methods during normal operating hours.

### What Can Be Purged

There are 4 possible objects that can be purged by configuring a retention policy, and each can additionally be filtered by release status, build status, and/or environment:

-   **Releases** - purges all filtered release data including promotions, builds, executions, and logs
-   **Builds** - purges all filtered build data including promotions, executions, and logs
-   **Execution Logs** - purges all execution logs associated with specific environments
-   **Artifacts** - purges artifact files from disk

:::(Warning)
Purging either Releases and Builds **does not** purge artifacts from disk for those builds. To clean up the artifact zip files from disk, a second retention policy must be added with the "purge orphaned artifacts" option enabled.
:::

### Retention Filtering & Options 

Retention policies perform purges only when the criteria specified in a particular policy are met. There are 7 main filters that apply to most purge objects:

-   **Application**
-   **Application Group**
-   **Workflow**
-   **Environment**  *(applies to purging logs only)*
-   **Time of Day**
-   **Release Status**
-   **Build Status**  *(does not apply to purging releases)*

:::(Warning)
Purging either Releases and Builds **does not** purge artifacts from disk for those builds. To clean up the artifact zip files from disk, a second retention policy must be added with the "purge orphaned artifacts" option enabled.
:::


Beyond these default filter options, you can also configure additional retention filters that prevent certain items from being purged, whether they match the filters or not:

-   **Count** - keep the last N items
-   **Days** - purge items only if they are older than N days
-   **Size** - keep at least the last N MB artifacts/logs

In addition, there is an option for the purge**Build Artifacts** type that also purge artifacts from disk that are no longer referenced in BuildMaster. This setting ignores any filters that may have been applied and always deletes all orphaned artifacts.

### Immediate Purge

Retention policies can also be forced to execute immediately, bypassing any day/time window restrictions.