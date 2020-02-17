---
title: High Availability & Load Balancing
subtitle: High Availability and Load Balancing
sequence: 300
keywords: proget, installation
show-headings-in-nav: true

---
ProGet's distributed architecture allows you to use any number of servers for both load-balancing and automatic failover purposes, such that one server node can take over in the event of any other server failure or outage.

  There are five key components to a High Availability configuration.

  ### Load Balancer {#load-balancer data-title="Load Balancer"}

The load balancer will direct traffic bound for the ProGet web application, evenly distributing traffic during heavy load times.

ProGet was built to be compatible with any load-balancing platform, whether software-based (such as HAProxy, NLP, or nginx) or appliance-based (such as F5, A10, Citrix).

{.docs}
- **Not Required: Session Affinity (sticky sessions)** - while you can enable this, it's not required and may simplify your traffic and load balancer configuration.
- **Common Machine Key** - you will need to use a common machine key amongst web nodes, see [Microsoft's documentation](https://msdn.microsoft.com/library/w8h3skw9) for how to configure this in your web.config or machine.config files

If you are new to load balancing, Microsoft's [Network Load Balancing Cluster](https://technet.microsoft.com/en-us/library/cc771008.aspx) is relatively easy to configure.

### Web Node Configuration {#web-node-configuration data-title="Web Node Configuration"}

Any number of servers may be configured as web nodes; to ensure high-availability, at least three nodes are required. More may be added to distribute load and ensure rapid responses.

To configure a web node, see the [manual installation instructions](/docs/proget/installation/manual) and follow only the web application instructions.

  Once complete, from any web server, click on the **Configure Load Balancing** link on the ProGet administration page, then click on the **Enable Load Balancing** button.

### Shared Storage {#shared-storage data-title="Shared Storage"}

ProGet is compatible with any type of common storage that web and indexing nodes can access, whether software-based (SAMBA share, Windows Server Storage Spaces, etc) or appliance-based (dedicated NAS). The only requirement is that the storage is readable and writeable by all nodes.

### Database Cluster {#database-cluster data-title="Database Cluster"}

ProGet can work with any SQL Server Cluster configuration, regardless of the failure detection mechanism and failover policy. SQL Server's Database Mirroring may also be used, although note that Microsoft has depricated the feature in favor of Always On Availability Groups.

### Indexing Nodes {#indexing-nodes data-title="Indexing Nodes"}

Any number of indexing nodes may be configured; to ensure high-availability, at least two nodes are required.

To configure an indexing node, see the [manual installation instructions](/docs/proget/installation/manual) and follow only the service instructions.

Once complete, from any web server, click on the **Configure High Availability Mode** link on the ProGet administration page, then click on the **Enable High Availability Mode** button.

Once enabled, the *Indexing Cluster Health* box on the administration page will replace the *Service Status* box. Clicking on the *More Info* link will display the Indexing Cluster Status page. On this page, you can see the status of every server in your cluster, as well as which server is currently the primary server. If a server is no longer in your cluster, you may delete it by clicking the appropriate red X button.
