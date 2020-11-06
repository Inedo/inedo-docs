---
title: Cluster Management
subtitle: Cluster Management in ProGet
sequence: 550
keywords: proget, administration, ha, high availability, load balancing, automatic failover, failover
---

Cluster Management allows you to monitor your ProGet server components configured for [load balancing and automatic failover](/docs/proget/installation/load-balancing-and-automatic-failover).  Starting in ProGet 5.3.16 you cane manage your ProGet cluster from a single page in ProGet by navigating to _Administation -> ProGet Cluster Overview -> More Info_. 

::: {.attention .best-practice}
This feature is available only in [ProGet Enterprise](https://inedo.com/proget/enterprise/). Compare [features by edition](/docs/proget/administration/license) or [request a quote](https://inedo.com/proget/pricing/request-quote). {.info}
:::

## Cluster Configuration {#cluser-configuration}

The cluster configuration section displays your current ProGet cluster settings and your primary and backup service components.

The cluster configuration displays:
- How many servers are in the cluster.
- If load balancing is enabled.
- If automatic failover is enabled.
   - You can also configure your service components' heart beat frequency and stale timeout.
- What server has the primary service component.
- What server(s) contain backup service components. _Note: Backup service components are only available when automatic failover is enabled._

## Servers in Cluster {#servers}

The servers in the cluster section lists which servers and components are included in the ProGet server cluster.

The server table displays:
- The server name.
- The operating system and version.
- The IP address configured on the server.
- The ProGet components installed on the server. (Web & Service)
- The health and verification status of each component.
  - The current heartbeat status.
  - If the software version is in sync.
  - If the encryption key is in sync.
  - If the machine key is in sync (Web component only).

You can also manually remove a component from the cluster if it is no longer included in the cluster.

You will also see a warning or an error symbol next to the server and component if the component has entered a stale state.  The web component stale timeout is based on a 24 hour window.  The service component's stale timeout is based on the configuration setting configured to the right of `automatic failover`.  

_Note: You can manually add components to test what this page would look like when a ProGet cluster is configured by clicking the `manually add a component` link at the bottom of the page._

## Troubleshooting

#### Why is my machine key showing a decryption failure?

This will occur when the machine key is not synchronized across your web servers.  See the [Load Balancing & Automatic Failover](/docs/proget/installation/load-balancing-and-automatic-failover#configure-web-node) guide to configure this setting.

#### Why is my Encryption showing a decription failure?

This will occur when the encryption key is not synchronized across your web servers.  The encryption key is stored in your [ProGet configuration file](/docs/proget/installation/config-files) and needs to be the same on all servers.

#### Why is my web component showing as stale when it is up and running?

The heartbeat on the web component will only update when web requests hit the ProGet web server.  If there is a 24+ hour break, like over a weekend or sticky session within a load balancer, where traffic is not routed to the web server, the web component may show a stale state.