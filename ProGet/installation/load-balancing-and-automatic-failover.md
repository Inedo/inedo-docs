---
title: Load Balancing & Automatic Failover
subtitle: Configuring High Availability
sequence: 400
keywords: proget, installation
show-headings-in-nav: true
---

ProGet's distributed architecture allows you to use any number of servers for both load-balancing and automatic failover purposes, such that one server node can take over in the event of any other server failure or outage.
::: {.attention .best-practice}
This feature is available only in paid ProGet editions. Compare [features by edition](/docs/proget/administration/license) or [request a quote](https://inedo.com/proget/pricing/request-quote). {.info}
:::

## Load Balancing {#load-balancing data-title="Load Balancing"}

Load Balancing is the process of distributing network traffic across multiple servers. This can improve ProGet's performance in high traffic environments. This is also required to configure automatic failover (High Availability or "HA"). 

Load balancing requires three components to be configured:
{.docs}
- Load Balancer
- Web Node & ProGet Service Setup
- Shared Storage

### Load Balancer {#load-balancer}

Like an air traffic controller, the load balancer will direct traffic bound for the ProGet web application, evenly distributing traffic during heavy load times.

ProGet was built to be compatible with any load balancing platform, including both software-based (such as HAProxy, NLP, or nginx) and appliance-based (such as F5, A10, Citrix) platforms. Each load balancer can be configured differently, so please consult the manufacturer's documentation for configuring the load balancer to work with IIS.

**Session Affinity (sticky sessions) are not required.** While you can enable this, it may simplify your traffic and load balancer configuration not to use them.

If you are new to load balancing, Microsoft's [Network Load Balancing Cluster (NLB)](https://technet.microsoft.com/en-us/library/cc771008.aspx) is relatively easy to configure. See the [ProGet & Microsoft NLB Guide](/docs/proget/installation/installation-guide/load-balanced) for installing and configuring NLB and ProGet.


### Web Node & ProGet Service Configuration {#web-node}

Any number of servers may be configured as web nodes. To ensure load balancing and high-availability, at least three nodes are required, but more may be added to distribute load and ensure rapid responses.

Load balancing is only supported when using IIS for hosting ProGet.

#### Web Node Prerequisites

Required settings are outlined in [IIS Roles & Features](/docs/various/iis/roles-and-features).

#### Configuring a Web Node {#configure-web-node}

Follow the [ProGet Installation Guide](/docs/proget/installation/installation-guide) for installing each web node using the Inedo Hub. 

::: {.attention .best-practice}
When installing ProGet on each web node, make sure to point each install to the same database by using the same database connection string for each.
:::

On each web node, configure a common machine key. See [Microsoft's documentation](https://msdn.microsoft.com/library/w8h3skw9(v=vs.100).aspx) for how to configure this in your web.config or machine.config files.

Machine keys can also be configured within the IIS Manager. To do this:
{.docs}
- On the first web node, select the ProGet website
  - Double click Machine Key
    - Use validation method SHA1 and Auto Encryption method
    - Deselect any check boxes 
    - Click _Generate Keys_
    - Click _Apply_ on the right panel; this will update the web.config with a machine key
- On the remaining web nodes, select the ProGet website
  - Double click Machine Key
    - Use validation method SHA1 and Auto Encryption method
    - Deselect any check boxes 
    - Enter the _Validation key_ and _Decryption key_ that was generated on the first web node 
    - Click _Apply_ on the right panel; this will update the web.config with a machine key

Once complete, from any web server, click on the **Configure Load Balancing** link on the ProGet administration page, then click on the **Enable Load Balancing** button.

#### Configuring the ProGet Service{#configure-service}

There is no additional installer for the ProGet service because it comes installed automatically with the InedoHub when configuring your [Web Node](#configure-web-node).

For load balanced installations that are not configured for High Availability, only one node should be running the ProGet service. On the remaining servers, stop the ProGet service and set it to `Disabled` in the service properties.

For HA installations, all web nodes should be running the ProGet service. HA will need to be [configured](#indexing-nodes) in ProGet.

#### Configure the Inedo Service Messenger {#service-messenger}

The service messenger is a component of ProGet that enables simple communication between web and service nodes. By default, it uses a named pipe, but in a load-balanced configuration, it needs to be configured to use TCP instead. The messenger is optional, but certain parts of ProGet's web interface may be slightly degraded without it. See the documentation on the [Service Messenger](/docs/proget/installation/installation-guide/service-messenger) for installation and configuration.

### Shared Storage {#shared-storage}

ProGet is compatible with any type of common storage that web and indexing nodes can access, whether software-based (e.g., SAMBA share, Windows Server Storage Spaces, etc.) or appliance-based (e.g., dedicated NAS). The only requirement is that the storage is readable and writeable by all nodes. Storage can be configured at the feed or server level.

#### Server-level Shared Storage

To configure server-level shared storage, update the following values in _Administration > Advanced Settings_:
{.docs}
- Storage.PackagesRootPath
- Storage.AssetLibrary
- Storage.DebianPackagesLibrary
- Storage.DockerBlobStorageLibrary
- Storage.DockerRepositoryLibrary
- Storage.HelmPackagesLibrary
- Storage.MavenArtifactLibrary
- Storage.NpmPackagesLibrary
- Storage.ProGetPackagesLibrary
- Storage.PypiPackagesLibrary
- Storage.RpmPackagesLibrary
- Storage.RubyGemsLibrary
- Storage.VsixLibrary

For cloud storage options, see [Cloud Storage](/docs/proget/advanced/cloud-storage).

#### Feed-level Shared Storage

To configure storage at a feed level, first navigate to the _Manage Feed_ page for each feed. Click _change_ on the right side of the _Disk Path_ property.

For cloud storage options, see [Cloud Storage](/docs/proget/advanced/cloud-storage).

## High Availability {#ha data-title="High Availability"}

High Availability (HA) ensures that ProGet can function without interruption when one or more web nodes are not operating. Automatic failover (HA) requires [load balancing](#load-balancing) to be configured.

Once load balancing has been configured, the following components will need to be configured:
{.docs}
- Database Cluster
- Indexing nodes


### Database Cluster {#database-cluster}

ProGet can work with any SQL Server Cluster configuration, regardless of the failure detection mechanism and failover policy. SQL Server's Database Mirroring may also be used, but note that Microsoft has deprecated the feature in favor of AlwaysOn Availability Groups. 

Unless the database is configured to perform its own replication, connecting to more than one SQL Server database would require additional ProGet Enterprise licenses. You would then connect the instances via [feed replication](/docs/proget/advanced/feed-replication).

Although a database cluster is not technically required for HA, it is strongly recommended. A single SQL Server node creates a single point of failure within your HA setup. 

### Indexing Nodes {#indexing-nodes}

Any number of indexing nodes may be configured, but to ensure HA, at least two nodes are required.

#### Configuring High Availability 

First [configure](#configure-service) the ProGet services for HA. Once complete, from any web server, click on the **Configure High Availability Mode** link on the ProGet administration page, then click on the **Enable High Availability Mode** button.

Once enabled, the *Indexing Cluster Health* box on the administration page will replace the *Service Status* box. Clicking on the *More Info* link will display the Indexing Cluster Status page. On this page, you can see the status of every server in your cluster, as well as which server is currently the primary server. If a server is no longer in your cluster, you may delete it by clicking the appropriate red X button.
