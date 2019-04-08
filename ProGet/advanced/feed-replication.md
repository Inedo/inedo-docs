---
title: Feed Replication
subtitle: Feed Replication
sequence: 200
keywords: load balanced, automatic failover
show-headings-in-nav: true
---

ProGet Enterprise offers users the ability to replicate feeds across multiple ProGet Servers, both locally and globally.

Implementing multi-site replication requires specific feed configurations: at least one feed must be configured as a replication server, and one or more feeds configured as a replication client.

## Configuration {#configuration data-title="Configuration"}

Feed replication is configured on a per-feed basis on the Feed Management page and requires the *Manage Feed* permission. A feed may be configured as a replication server, a replication client, or in more advanced scenarios, as both. 

##### Source URL:

The source URL is the endpoint exposed by the replication server, and must be specified in the replication client configuration.

##### Sync Tokens:

Configuring a feed as a replication server will require a sync token to be added/generated which replication clients must supply along with the replication server URL. The sync token should be treated as a secret, as it then allows any replication client to connect to the feed.

### Replication Client

Replication clients are the nodes where the logic is executed to determine which changes should be propagated based on a feed state returned from a replication server.

##### Options:

 - **Disabled** - the feed will not query any replication server feeds for changes
 - **Only apply external changes to local feed** *(pull-only)* - the feed will query a replication server feed for changes, and only pull changes without affecting the replication server feed
 - **Only apply local changes to external feed** *(push-only)*- the feed will query a replication server feed for changes, and only push changes to it without affecting the local feed
 - **Mirror external feed** - the feed will query a replication server feed for changes, apply the changes locally, then push any local changes to the replication server feed resulting in identical feeds in both

### Replication Server 

Replication servers are nodes that are periodically queried by replication clients and respond with a feed state.

##### Options:

 - **Disabled** - replication clients are not permitted to connect to the feed
 - **Allow external feeds to replicate from this feed** *(read-only)*- replication clients may connect to the feed, pull changes, but not push changes back
 - **Allow local feeds to be changed by external feeds** *(read/write)*- replication clients may connect to the feed, pull changes, and push changes to it

### Valid Configurations

The following table describes the possible configurations *(client configuration is per column, server configuration per row)*:

|  | Disabled | Pull-only | Push-only | Mirror |
|-|-|-|-|-|
| **Disabled**   | - | <span class="info-block error">Invalid</span> | <span class="info-block error">Invalid</span> | <span class="info-block error">Invalid</span> |
| **Read-only**  | - | <span class="info-block success">Valid</span> | <span class="info-block error">Invalid</span> | <span class="info-block warning">Warning</span> |
| **Read/Write** | - | <span class="info-block success">Valid</span> | <span class="info-block success">Valid</span> | <span class="info-block success">Valid</span> |

Invalid configurations will be logged by the client in the corresponding execution logs, and by the server in the Diagnostic Center.

## Architecture & Examples {#architecture data-title="Architecture & Examples"}

### Full Replication & Mirroring {#mirroring}

A simple example would be the complete mirroring of a single feed across multiple ProGet installations with each configured as follows:

| Node | Client | Server |
|-|-|-|
|Tokyo | Mirror external feed | Disabled  |
|Los Angeles | Disabled | Allow local feeds to be changed by external feeds |
|Cleveland | Mirror external feed | Disabled  |

![](/resources/documentation/proget/administration/basic-feed-rep.png)

This setup will propagate all package pushes or deletes across all 3 feeds.

### Partial Replication {#partial}

A more complex example involves configuring an installation to act as both a replication client and server. In this following example, a feed in each city is configured as follows:

| Node | Client | Server |
|-|-|-|
|Los Angeles | Disabled | Allow local feeds to be changed by external feeds |
|New York | Mirror external feed | Allow external feeds to replicate from this feed |
|Tokyo | Only apply external changes to local feed | Disabled  |
|Miami | Only apply external changes to local feed | Disabled  |
|Berlin | Only apply external changes to local feed | Disabled  |

 ![](/resources/documentation/proget/administration/complex-feed-rep2.png)

The above configuration exhibits the following behavior:
 - feeds in Los Angeles and New York are fully mirrored; any pushes or deletes to either will propagate in bi-directionally
 - feeds in Tokyo, Miami, and Berlin will periodically pull pushes or deletes from New York (and by extension, Los Angeles)
 - pushes or deletes to feeds in Tokyo, Miami, and Berlin will *not* be propagated to New York (or by extension Los Angeles)

## Best-Practices {#best-practices data-title="Best Practices" }

Based on usage and support tickets we have encountered in the past, we recommend the following guidelines:

 - <span class="info-block success">DO</span>: ensure all ProGet installations involved in replication are running the exact same version of ProGet, or at the very least, the same minor version (e.g. 5.2.x)
 - <span class="info-block success">DO</span>: prefer using [connectors](/support/documentation/proget/core-concepts/feeds/connectors) to an external feed instead of read-only replication when possible
 - <span class="info-block error">DO NOT</span>: configure replication to another feed in the same instance of ProGet
 - <span class="info-block error">DO NOT</span>: configure two instances to both be replication clients and servers pointing to each other
- <span class="info-block error">DO NOT</span>: use replication for availability purposes if you are already using a package store that provides its *own* replication, for example if you are using the [AWS package store](/support/documentation/proget/advanced/cloud-storage#configure) with [cross-region replication](https://docs.aws.amazon.com/AmazonS3/latest/dev/crr.html)

## Troubleshooting {#troubleshooting data-title="Troubleshooting" }

Perhaps the most common issue related to replication is that ProGet Enterprise (or trial) is required for each node involved in the replication, otherwise an error will be generated and replication will not occur.

### Replication Status & Overview

To view the list of feeds that have replication configured, visit the Replication Overview page (`Administration` > `Replication Overview`).

### Logging

#### Clients

The logs for replication clients can be found on the Executions page. To view them, visit the `Administration` > `Executions` page, then filter by "Feed Replication".

#### Servers

The logs for replication servers will be found in the Diagnostic Center. Visit `Administration` > `Diagnostic Center` > `View Log Messages` and filter the category by "Feed Replication" to view any error messages.

Note that by default, only replication server errors are logged. To enable logs for successful connections, visit the Advanced Settings page and set `Diagnostics.MinimumLogLevel` to `0` to enable debug messages. Changing this setting requires the web application to be restarted. It is recommended that this setting only remain at `0` temporarily, then reset back to the default value of `20`.

### Client & Server Options Missing

The replication client/server distinction was added in ProGet v5.2, and previous versions only allowed one or the other to be configured, and not both. The options "replicate from external feed" and "allow other feeds to replicate with this one" are equivalent to client and server respectively.