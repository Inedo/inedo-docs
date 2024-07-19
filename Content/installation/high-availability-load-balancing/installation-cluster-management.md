---
title: "Managing your Cluster"
order: 2
---

The Cluster Management Page (found under Admin > Cluster Overview) allows you to monitor your instance server components that are configured for [load balancing and automatic failover](/docs/installation/high-availability-load-balancing/high-availability-load-balancing). 

::: (INFO)
Cluster Management is available from ProGet 5.3.16, Otter v3, and BuildMaster v7. 
:::

## Cluster Configuration 

The Cluster Configuration pane displays the settings of your current cluster and your primary and backup service components.

- How many servers are in the cluster.
- If load balancing is enabled.
- If automatic failover is enabled

You can also configure your the heart beat frequency and stale timeout.

*On Inedo products versioned 2022 and earlier, a "primary server" and "backup server" were also displayed. This was removed in 2023, as work is now distributed across servers and this distinction is no longer meaningful.*

## Servers in Cluster 

The Servers in Cluster section lists which servers and components are included in the server cluster.

The server table displays:
- The server name.
- The operating system and version.
- The IP address configured on the server.
- The application components installed on the server. (Web & Service)
- The status of each component and its verification.
  - The current heartbeat status.
  - If the software version is in sync.
  - If the encryption key is in sync.
  
You can also manually remove a component from the cluster if it is no longer in the cluster.

You will also see a warning or error icon next to the server and the component if the component has reached an outdated state. The timeout for the web component is based on a 24-hour window. The timeout for the service component is based on the configuration setting that appears to the right of `automatic failover`.  

## Troubleshooting

#### Why am I seeing "Cannot connect to service messenge"?
The service messenger is a nonessential component that improves the user experience of ProGet by displaying real-time information from service nodes. When it's not available, information in the Web UI may be delayed by a few seconds to a minute.

To get the messenger working, verify that the configured port (default is 33237) is accessible on the server node you clicked on. Then, restart the ProGet service and click the "reconnect" button in the UI.

#### Why is my Encryption showing a decription failure?

This will occur when the encryption key is not synchronized across your web servers. The encryption key is stored in your [configuration file](/docs/installation/configuration-files) and needs to be the same on all servers.

#### Why is my web component showing as stale when it is up and running?

The web component heartbeat is updated only when web requests are received on the web server. If there is an interruption of more than 24 hours, such as over a weekend or a sticky session within a load balancer where traffic is not routed to the web server, the web component may display an outdated status.

#### Why is my machine key showing a decryption failure?

In ProGet 5.3 and earlier, this will occur when the machine key is not synchronized across your web servers. See the [Load Balancing & Automatic Failover](/docs/installation/high-availability-load-balancing/high-availability-load-balancing) guide to configure this setting.