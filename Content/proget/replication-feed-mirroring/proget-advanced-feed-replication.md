---
title: "Feed Replication & Mirroring Overview"
order: 1
---

ProGet's replication capabilities allow you to synchronize packages, containers, and assets across multiple ProGet instances. This can help implement a variety of scenarios and complex network architectures, including:
* [Simplifying disaster recovery](/docs/proget/replication-feed-mirroring/proget-howto-utilize-feed-replication-for-disaster-recovery)
* [Easily distributing content for edge computing](/docs/proget/replication-feed-mirroring/proget-howto-replicate-edge-locations)
* [Multisite development & federated development teams](/docs/proget/replication-feed-mirroring/proget-howto-federated-development)

## Configuration
A "replication" requires at least two instances of [ProGet Enterprise Edition](https://inedo.com/proget/enterprise) and at least one feed of the same type between those instances. However, you can configure replication with multiple feeds, and you can also configure multiple replications on a single instance. In this way, you can design complex content distribution schemes, such as [multi-hub private content delivery network](/docs/proget/replication-feed-mirroring/proget-howto-replicate-edge-locations).

### Communication Mode
At least one instance is configured for "outgoing" communication (similar to how a web browser accesses a web server), the other for "incoming" communication (like a web server responds to a web browser).

::: (INFO)
Prior to ProGet v6, incoming replication configurations were called replication servers and outgoing replication configurations were called replication clients.
:::

Behind the scenes, incoming communication is handled by the Replication API endpoint hosted on the ProGet Web Application, just like all other API endpoints. This means that access via HTTP/S occurs in the same way that your browser and other client tools interact with ProGet.

Outgoing communication is handled by the ProGet Service application, which communicates with the other instance's Replication API endpoint. The "outgoing" instance is where most of the work is done, and where you'll find detailed replication logs.


### Replication Mode
Regardless of the communication mode you choose, you can configure how content (packages, containers, and assets) is synchronized between ProGet instances.

Each instance must be configured in one of the following ways:
* **Mirror Content**; Push and pull packages so that the feed is synchronized. The other instance should be configured to Mirror Content
* **Pull Content from other Instance** ; Download missing content from the other instance and delete content that was deleted from this instance. The other instance should be configured to Push Content.
* **Push Content to other Instance**; Upload missing content to the other instance and delete content that was deleted on this instance. The other instance should be configured to Pull Content.

You can also select the Custom/Advanced option, which allows more granular control over what is replicated.

### Multiple Replications
Instead of selecting multiple feeds in a single replication, you can configure multiple replications on a single instance. This is common to do when [distributing content for edge computing](/docs/proget/replication-feed-mirroring/proget-howto-replicate-edge-locations), but there are other reasons you may wish to do this.

* Use different sync tokens so you don't have to share them across feeds
* Replicate content from multiple other instances in outgoing mode
* Different replication modes for different feeds, e.g. some as pull and others as push

Whatever the reason, be careful when configuring different replications for the same feed. For example, if you pull packages from two different instances and delete content from two different instances, you may get unexpected results.

:::(Internal) (TODO)
## Propagating Content Deletions
Talk about “tombstone” records.
Explain how they work.
:::

## Best-Practices

Based on usage and support tickets we have encountered in the past, we recommend the following guidelines:

 - <span style="color: green;">DO</span>: ensure all ProGet installations involved in the replication use the exact same version of ProGet, or at the very least, the same minor version (e.g. 5.2.x)
- <span class="info-block error">DO NOT</span>: configure replication to a different feed in the same instance of ProGet
 - <span class="info-block error">DO NOT</span>: configure two instances to be both incoming and outgoing replication feeds that reference each other
- <span class="info-block error">DO NOT</span>: use replication for availability purposes if you are already using a package store that provides its *own* replication, for example if you are using the [AWS package store](/docs/proget/advanced-features/proget-advanced-cloud-storage#configure) with [cross-region replication](https://docs.aws.amazon.com/AmazonS3/latest/dev/crr.html)
 - <span class="info-block error">DO NOT</span>: configure an incoming replication feed to pull changes and then distribute the sync token to untrusted parties, as this effectively grants full access to the contents of the feed, which could contain poisoned packages, malware, etc.

## Troubleshooting 

Perhaps the most common issue related to replication is that ProGet Enterprise (or a trial version) is required for each server cluster involved in replication, otherwise an error is generated and replication does not occur.

### Replication Status & Overview

For a list of feeds for which replication is configured, see the Replication Overview page.

### Logging

#### Outgoing Replication Feeds

The logs for outgoing replication feeds can be found on the Executions page. To view them, visit the `Administration` > `Executions` page, then filter by "Feed Replication".

#### Incoming Replication Feeds

The logs for incoming replication feeds will be found in the Diagnostic Center. Visit `Administration` > `Diagnostic Center` > `View Log Messages` and filter the category by "Feed Replication" to view any error messages.

Note that by default, only incoming replication errors are logged. To enable logs for successful connections, visit the Advanced Settings page and set `Diagnostics.MinimumLogLevel` to `0` to enable debug messages. To change this setting, the web application must be restarted. It is recommended to leave this setting at '0' only temporarily and then set it back to the default value of `20`.

### Outgoing & Incoming Options Missing 

The distinction between outbound and inbound (client/server) replication was added in ProGet v5.2, and earlier versions only allowed configuration of one or the other option, not both. The options "replicate from external feed" and "allow other feeds to replicate with this feed" correspond to client and server, respectively.

## Docker

Docker feed replication is supported starting with ProGet 5.2.5 and has the following limitations:
- The release date and number of downloads are not synchronized.
- All blobs and manifests are synchronized, so retention rules must be set up on both sides of the synchronization.
- Deletion entries ("tombstones") are not recorded in Docker feeds, so images can only be added by feed replication but not removed; a retention rule must be configured to delete untagged images.

## Feed Replication (v6 and Earlier)
In ProGet v6 and earlier, feed replication was configured on the feed management page for each feed individually and required the Manage Feed permission (See [Feed Replication (v6 and Earlier)](https://web.archive.org/web/20231210002734/https://docs.inedo.com/v1/docs/proget-feed-replication-v6
). A feed could be configured for incoming replication requests, outgoing replication requests, or both in advanced scenarios.

