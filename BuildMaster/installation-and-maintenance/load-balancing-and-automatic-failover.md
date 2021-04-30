---
title: Load Balancing & Automatic Failover
subtitle: Configuring High Availability
sequence: 400
keywords: buildmaster, installation
show-headings-in-nav: true
---

BuildMaster's distributed architecture allows you to configure BuildMaster as a server cluster, over any number of servers for both load-balancing and automatic failover purposes, such that one server can take over in the event of any other server failure or outage. You can manage your cluster by using the [cluster management page in BuildMaster](/docs/buildmaster/administration/cluster-management).
::: {.attention .best-practice}
This feature is available only in [BuildMaster Enterprise](https://inedo.com/buildmaster/enterprise/). Compare [features by edition](/docs/buildmaster/administration/license) or [request a quote](https://inedo.com/buildmaster/pricing/request-quote). {.info}
:::

## Load Balancing {#load-balancing data-title="Load Balancing"}

Load Balancing is the process of distributing network traffic across multiple servers. This can improve BuildMaster's performance in high traffic environments. This is also required to configure automatic failover (High Availability or "HA"). 

Load balancing requires the following components to be configured:
{.docs}
- [Load Balancer](#load-balancer)
- [BuildMaster software (Website & Service)](#web-node)
- [Shared Storage](#shared-storage)
- [Shared Database](#database-cluster)

### Load Balancer {#load-balancer}

BuildMaster was built to be compatible with any load balancing platform, including both software-based (such as HAProxy, NLP, or nginx) and appliance-based (such as F5, A10, Citrix) platforms. Each load balancer can be configured differently, so please consult the manufacturer's documentation for configuring the load balancer to work with IIS.

::: {.attention .best-practice}
**Session Affinity (sticky sessions) are not required.** While you can enable this, it may simplify your traffic and load balancer configuration not to use them.
:::

If you are new to load balancing, Microsoft's [Network Load Balancing Cluster (NLB)](https://technet.microsoft.com/en-us/library/cc771008.aspx) is relatively easy to configure.


### Creating a BuildMaster Cluster {#web-node}

Any number of servers can be in a BuildMaster Cluster, and a BuildMaster Enterprise License allows for up to five servers per instance by default.

To ensure both load balancing *and* high-availability, at least three servers are required, and more may be added later to distribute load and ensure rapid responses.

Note that load balancing is only supported when using IIS for hosting BuildMaster.

#### Configuring a BuildMaster Server Cluster {#configure-web-node}

You can use the standard [BuildMaster Installation Guide](/docs/buildmaster/installation/installation-guide) to install BuildMaster on each server in the cluster.

::: {.attention .best-practice}
When installing BuildMaster on each server, make sure to point each install to the same database by using the same database connection string for each.
:::

You will need to make sure the `EncryptionKey` value in the BuildMaster configuration file is the same on all servers.  Please see our [Configuration Files](/docs/buildmaster/installation/config-files) documentation on where to find the configruation file and where to set the `EncryptionKey` value.

Once complete, from any web server, click on the **Configure Load Balancing** link on the BuildMaster administration page, then click on the **Enable Load Balancing** button.

::: {.attention .best-practice}
If you have a "BuildMaster Basic with Load Balancing" license, additional configuration is required.
Because one server can run the BuildMaster Service, on the remaining servers, stop the BuildMaster Service and set it to `Disabled` in the service properties.
:::

### Configuring the Inedo Service Messenger {#service-messenger}

The service messenger is a component of BuildMaster that enables simple communication between the BuildMaster Website and BuildMaster Service. By default (on a single server), it uses a named pipe, but in a load-balanced configuration, it needs to be configured to use TCP instead. The messenger is optional, but certain parts of BuildMaster's web interface may be slightly degraded without it. See the documentation on the [Service Messenger](/docs/buildmaster/installation/installation-guide/service-messenger) for installation and configuration.

### Configuring Shared Storage {#shared-storage}

BuildMaster is compatible with any type of common storage that all services (and the BuildMaster Website and BuildMaster Service on each) can access, whether software-based (e.g., SAMBA share, Windows Server Storage Spaces, etc.) or appliance-based (e.g., dedicated NAS). The only requirement is that the storage is readable and writeable by all servers.

#### Server-level Shared Storage

To configure server-level shared storage, update the `Artifacts.BasePath` value in _Administration > Advanced Settings_.

### Configuring Database Cluster {#database-cluster}

BuildMaster can work with any SQL Server Cluster configuration, regardless of the failure detection mechanism and failover policy. SQL Server's Database Mirroring may also be used, but note that Microsoft has deprecated the feature in favor of AlwaysOn Availability Groups. 

Unless the database is configured to perform its own replication, connecting to more than one SQL Server database would require additional BuildMaster Enterprise licenses. You would then connect the instances via [feed replication](/docs/buildmaster/advanced/feed-replication).

Although a database cluster is not technically required for HA, it is strongly recommended. A single SQL Server node creates a single point of failure within your HA setup. 


## Configure High Availability {#ha data-title="High Availability"}

High Availability (HA) ensures that BuildMaster can function without interruption when one or more servers are not operating. Automatic failover (HA) requires [load balancing](#load-balancing) to be configured.

#### Configuring High Availability 

From any BuildMaster server, click on the **Configure High Availability Mode** link on the BuildMaster administration page, then click on the **Enable High Availability Mode** button.

Once enabled, the *Cluster Health* box on the administration page will be shown above the *Service Status* box. Clicking on the *More Info* link will display the Indexing Cluster Status page. On this page, you can see the status of every server in your cluster, as well as which server is currently the primary server. If a server is no longer in your cluster, you may delete it by clicking the appropriate red X button.
