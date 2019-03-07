---
title: Feed Replication
subtitle: Feed Replication
sequence: 200
keywords: load balanced, automatic failover

---
ProGet Enterprise offers users the ability to mirror feeds across multiple ProGet Servers, both locally and globally.

  Implementing the multi-site replication requires specific feed configurations: one feed must be configured as *allow other feeds to replicate with* and one or more feeds configured to *replicate from external feed*.

This feature is available in paid and trial ProGet editions. {.info}

## Architecture {#architecture data-title="Architecture"}

A simple example would be the replication of a single feed across three ProGet servers: Los Angeles, Tokyo, and Cleveland. Los Angeles acts like the hub and is configured to *allow other feeds to replicate with* it, and Tokyo and Cleveland are configured to *replicate from external feed*.

![](/resources/documentation/proget/administration/basic-feed-rep.png)

A more complex example would be this: say you have multiple ProGet servers spread across different global locations, and you’d like to have a synchronized feed for all ProGet instances within your organization (New York City, Berlin, Cleveland, Tokyo, Los Angeles, Singapore, Seattle, and London). In this example, New York City is configured to *allow other feeds to replicate with* it and Berlin, Cleveland, Tokyo, Los Angeles, Singapore, Seattle, and London are configured to *replicate from external feed*.

 ![](/resources/documentation/proget/administration/complex-feed-rep.png)

 ## Configuration {#configuration data-title="Configuration"}

 Replication is configured on a feed by feed basis. When replication is enabled, feeds must be configured with the specified feed URL, and for replication from external, the authentication token must be entered. Since this feature is implemented at the feed level, you can decide which specific feed or feeds will be mirrored across instances, providing flexibility for specific organizational needs. If so your organization decides, you may mirror an entire instance of ProGet, including all of your external and in-house feeds, and in turn have access to all of the same packages on a virtually identical instance.

![](/proget/5.0/replicate-from.png){.screenshot}
