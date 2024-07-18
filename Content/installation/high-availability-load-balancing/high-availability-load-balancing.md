---
title: "Configuring High Availability & Load Balancing"
order: 1
---

The distributed architecture of ProGet, BuildMaster, and Otter allows you to configure an instance over any number of servers for both load-balancing and failover purposes. By doing so, any single server can take over the cluster in the event of server failure and outage. This can improve your Inedo product's performance in high-traffic environments. 

:::(Info)
Using the High Availability & Load Balancing feature requires an Enterprise-level license; i.e. [ProGet Enterprise](https://inedo.com/proget/enterprise/), BuildMaster Enterprise, or Otter Enterprise.
:::

You can [manage your cluster by using the Cluster Management page](/docs/installation/high-availability-load-balancing/installation-cluster-management) in your Inedo product.

## Prerequisites 
High Availability & Load Balancing  requires the following components to be configured:
- Load Balancer
- Shared Storage
- Shared Database
- Inedo Product  (Encryption Key & Settings)

In addition, an open TCP port (we recommend 33237) is recommended to allow for nodes within the cluster to communicate with eachother. 

### Selecting a Load Balancer
Your Inedo product is built to be compatible with any load balancing platform, including both software-based (such as HAProxy, NLP, or nginx) and appliance-based (such as F5, A10, Citrix) platforms. Each load balancer can be configured differently, so please consult the manufacturer's documentation for configuring the load balancer to work with IIS.

::: (INFO)
**Session Affinity (sticky sessions) is not required.** While you can enable this, it may simplify your traffic and load balancer configuration not to use them.
:::

#### Example:  Microsoft's Network Load Balancer 
If you are new to load balancing, Microsoft's Network Load Balancing Cluster (NLB) is relatively easy to configure. See [Using Microsoft Network Load Balancing (NLB)](/docs/installation/high-availability-load-balancing/high-availability-load-balancing) for guidance on how to use NLB with Inedo products.
:::

###  Database Server
You will need a stand-alone SQL Server that all servers in your clustercan connect to. This could be as simple as SQL Express, but we recommend a database cluster so that there's no single point of failure within your High-availability setup.

Your Inedo product can work with any SQL Server Cluster installation, regardless of the failure detection mechanism and failover policy. SQL Server's Database Mirroring may also be used, but note that Microsoft has deprecated that feature in favor of AlwaysOn Availability Groups.


## How to Create a Cluster 
Any number of servers can be in a cluster to start, and more servers may be added later to distribute load and ensure rapid responses.

::: (INFO) ()
A ProGet Enterprise License allows for up to five servers per instance by default.
:::

### Step 1: Install Product on Each Server

Each server must be running the same version of an Inedo product. You can use the standard installation guides for each Inedo product to install the software on each server in the cluster.

* [BuildMaster Installation Guide](/docs/buildmaster/installation-maintenance/buildmaster-installation-guide)
* [ProGet Installation Guide](/docs/proget/installation/installation-guide)
* [Otter Installation Guide](/docs/otter/installation-upgrading/otter-installation-guide)

The easiest method is usually the Inedo Hub.

:::(Warning)
When installing your Inedo product on each server, make sure to point each install to the same, shared database by using the same database connection string for each.
:::

### Step 2: Configure the EncryptionKey 
After installation, make sure the `EncryptionKey` value is the same across all servers. 

For Windows, see see [Installation Configuration Files](/docs/installation/configuration-files) to find the configuration file and where to set the `EncryptionKey` value.

For Linux/Docker, this involves setting a `PROGET_ENCRYPTION_KEY`environment variable; see [Supported Environment Variables](/docs/installation/linux/supported-environment-variables).

### Step 3: Configure Shared Storage 
Your Inedo product is compatible with any type of common storage that all services can access, whether software-based (e.g., SAMBA share, Windows Server Storage Spaces, etc.) or appliance-based (e.g., dedicated NAS). The only requirement is that the storage is readable and writeable by all servers.

#### Server-level Shared Storage
To configure server level shared storage, update the following:
| Inedo Product | Value|
| --- | --- |
| BuildMaster | `Artifacts.BasePath` value in _Administration > Advanced Settings_. |
| ProGet | `Storage.PackagesRootPath` value in _Administration > Advanced Settings_. |
| Otter | *No configuration is required* |

#### (ProGet) Feed-level Shared Storage
To configure storage at a feed level, first, navigate to the _Manage Feed_ page for each feed. Click _change_ on the right side of the _Disk Path_ property. For cloud storage options, see [Cloud Storage](/docs/proget/advanced-features/proget-advanced-cloud-storage).

### Step 4: Enable in Product
Once complete, from any web server, navigate to Administration, and click "More Info" under the "Cluster Overview" section. Clicking the "configure" link will open a dialog that allows you to check "Enable High-Availability (Automatic Fail-over)". 

After saving configuration, restart the ProGet service on each of the nodes for the change to take effect.

#### Advanced Cluster Configuration Options
Clicking on the "Advanced" tab of the configuration will allow you to set additional values for your server cluster.

![installation-cluster-configure](/resources/docs/installation-cluster-configure.png){height="" width="50%"}

Note that changing these settings will also require a restart of both the web and service nodes on each server in the cluster.

### Step 5: Verify Health & Configuration
Once enabled, the *Indexing Cluster Health* box on the administration page will be shown above the *Service Status* box. Clicking on the *More Info* link will display the Indexing Cluster Status page. On this page, you can see the status of every server in your cluster.

If a server is no longer in your cluster, you may delete it by clicking the appropriate red X button.

For troubleshooting tips, see [Managing your Cluster](/docs/installation/high-availability-load-balancing/installation-cluster-management).


## Configuring Version 2022
This guide was written for Inedo products versioned 2023 and newer. If you're using ProGet 2022, BuildMaster 2022, or Otter 2022, you'll need to take a few different steps.

### Enabling in Product (Step 4)
You'll need to enable Load Balancing and High Availability prior to seeing the Cluster Overview page in the administration section. To do this:
1. click on the **Configure Load Balancing** link on your Inedo product’s administration page, then click on the **Enable Load Balancing** button
2. click on the **Configure High Availability Mode** link on the software’s administration page, and then click on the **Enable High Availability Mode** button.

### Configure Service Messenger (Step 4)
You'll also need to configure the "service messenger", which is used to allow nodes within the cluster to communicate directly with each other. It's not a critical component, and when it's not available, information in the Web UI may be delayed by a few seconds to a minute.

We recommend using port `33237` for this messenger. To configure this port:

1. Set the `Service.MessengerEndpoint` configuration value (Admin > Advanced Settings) to `tcp://*:33237`
2. Click the `[change]` button next to each service node, and enter `tcp://«node-ip-address»:33237`, where `«node-ip-address»` is the IP Address of the service displayed on the page

Then, restart the service and all web nodes.

### ProGet 5.3 and Earlier
ProGet 5.3 and earlier required thata common "machine key" was configured on each server.  See [Microsoft's documentation](https://msdn.microsoft.com/library/w8h3skw9(v=vs.100).aspx) for how to configure this in your web.config or machine.config files.

Machine keys can also be configured within the IIS Manager. To do this:
- On the first server, select the ProGet website
  - Double click Machine Key
    - Use validation method SHA1 and Auto Encryption method
    - Deselect any check boxes 
    - Click _Generate Keys_
    - Click _Apply_ on the right panel; this will update the web.config with a machine key
- On the remaining servers, select the ProGet website
  - Double click Machine Key
    - Use validation method SHA1 and Auto Encryption method
    - Deselect any check boxes 
    - Enter the _Validation key_ and _Decryption key_ that was generated on the first server
    - Click _Apply_ on the right panel; this will update the web.config with a machine key