---
title: ProGet Load-balanced Installation
sequence: 400
show-headings-in-nav: true
keywords: proget, installation, load-balanced
---

::: {.attention .best-practice}
These step-by-step instructions were developed by Scott Cusson (Senior Release Engineer, Symbotic), primarily as a guide for internal use. We're sharing it as a reference guide that may help you set-up your load-balanced or high-availability installation. See [load balancing and automatic failover](/docs/proget/installation/load-balancing-and-automatic-failover) for more information.
:::

## Guideline to setting up ProGet to support Load Balancing

Below is a guideline for setting up a cluster and its various web servers in a Network Load Balancing (NLB) environment to support the Load Balancing feature in ProGet. This uses Microsoft Window's NLB feature as a load balancer, and all machines in this cluster are VMs.

#### These steps were used to update a single existing ProGet instance.

## Prerequisites:

*   ProGet Enterprise license
*   System user account with Administrator rights to all machines in the cluster
*   Admin access to ProGet

## Needed by IT:

*   Windows Server 2012 R2 (minimum) machines with 2 NICs (one dedicated for NLB cluster) each with static IPs to host the ProGet website. Example machine names in italics.
    *   One machine per web node
        *   _ProGet_Webnode1_ (this will also serve as the indexing server)
        *   _ProGet_Webnode2_
        *   _ProGet_Webnode3_
    *   Machine to set up the NLB cluster
        *   _ProGet_NLB_
    *   Database (DB) server with SQL Server 2012 (minimum) for the ProGet DB and associated package data.
        *   _ProGet_DB_
            *   SQL installed with the System user. This user needs also to be the DB owner and sysadmin.
            *   Package Data location: C:\ProgramData\ProGet\Packages
            *   Share the Packages folder. The web nodes will be configured to access:  
                `\\<_ProGet_DB_>\Packages`
*   Cluster IP with Subnet mask (see [Setting up the Cluster](#setting-up-cluster) below) mapped to the Internet name and company domain (ex. http://nuget.srv.companyname.corp/).
*   Address Resolution Protocol (ARP) entry using the cluster IP to the switch that routes into the NLB VLAN

**Install on each web node via the Server Manager > Add Roles and Features as the System user:**

*   IIS (Web Server) under Server Roles using default selections
*   Network Load Balancing under Features
*   .Net 4.5 under Features (for IIS machine key gen). Install all options

## Setting up the Cluster:

*   RDP as the System user into _ProGet_NLB_
*   Open the NLB manager (Control Panel\All Control Panel Items\Administrative Tools\Network Load Balancing Manager)
    *   Create a new cluster
    *   Use the following as a guide to setup NLB: Microsoft's [Network Load Balancing Cluster](https://technet.microsoft.com/en-us/library/cc771008.aspx).
        *   (Session Affinity (sticky sessions) is not required.)

### Cluster properties:

1.  **IP** - provided by IT. 10.0.0.100
2.  **Subnet mask** - provided by IT. 255.255.250.0
3.  **Internet name** - provided by IT. NUGET.SRV
4.  **Operation mode** - Multicast
5.  **Port range** - 80 to 80
6.  **Protocols** - TCP
7.  **Filtering mode** - Multiple host\Single | None (None option preferred)
    *   None (preferred); indicates that any host in the cluster which matches to the port rule can handle the client request
    *   Multiple host\Single; indicates that one host in the NLB cluster can process traffic from the same client. The requests are basically transmitted to the same host every time NLB reads the IP address.

## Setting up the ProGet web nodes:

Download the latest ProGet manual installer ([https://inedo.com/proget/versions](/proget/versions)) and on each web node:

*   Extract the installer zip
    *   Copy the contents of `Proget-WebApp.zip` to `C:\ProGet\WebApp`
    *   On _ProGet_Webnode1_ only, copy the contents of Proget-Service.zip to `C:\ProGet\Service`
*   Open the IIS manager
    *   Create the ProGet website following the "Web Application Installation: For IIS hosted applications, there are 2 additional steps" section on [/docs/proget/installation/installation-guide/manual](/docs/proget/installation/installation-guide/manual)
    *   Websites and App Pools must be running as the System user.
*   Update the Bindings with
    *   Cluster IP - provided by IT (ex. 10.0.0.100)
    *   Port 80
    *   Host name – provided by IT (ex. nuget.srv.companyname.corp)

Configure `C:\ProGet\WebApp\web.config` on each of the web nodes.

*   In the IIS manager
    *   Select the ProGet website
    *   Double click Machine Key
        *   Use validation method SHA1 and Auto Encryption method.
        *   Deselect any check boxes and click Generate Keys and Apply on the right pane.
            *   This will update the web.config with a machine key.
        *   Modify `C:\ProGet\WebApp\Web.config` with notepad and in the appSettings section, add:
            *   `<add key="InedoLib.DbConnectionString" value="Data Source=<_ProGet_DB_>;Initial Catalog=ProGet;Integrated Security=True;Max Pool Size=200" />`
            *   `<add key="ProGetConfig.Storage.PackagesRootPath" value="\\<_ProGet_DB_>\Packages" />` to point ProGet to the nuget package location.
        *   Overwrite the web.config on the other web nodes and restart the ProGet websites

## Update ProGet to enable Load Balancing:

*   Login to ProGet administration
*   Click on Configure Load Balancing
    *   Enable
*   Click on Advanced Settings
    *   Update PackagesRootPath from `C:\ProgramData\ProGet\Packages` to `\\<_ProGet_DB_>\Packages` if needed
    *   Update remaining Storage. Value entry paths from `C:\ProgramData\ProGet\Packages\` to `\\<_ProGet_DB_>\Packages\`
    *   Any Extension. Value entries need to remain at `C:\ProgramData\ProGet`. Unknown why, but this means that any extensions (InedoCore, AWS, Azure, etc.) need to be in these locations on all web nodes and manually updated.
    *   Save Changes

## Install the ProGet Service to perform indexing and other background tasks:

On _ProGet_Webnode1_ only, create file `%PROGRAMDATA%\Inedo\SharedConfig\ProGet.config` with the content:

```
<InedoAppConfig>
    <ConnectionString>Data Source=<_ProGet_DB_>;Initial Catalog=ProGet;Integrated Security=True;Max Pool Size=200</ConnectionString>
    WebServer Enabled="false" Urls="http:<_Cluster_IP_>:80:<_Host_name_>;http://localhost:80" />
</InedoAppConfig>
```

In an Administrator command window, change directory to `C:\ProGet\Service` and run "Service.exe install" to install the service.

Open the Services (Control Panel\All Control Panel Items\Administrative Tools > Services) and change the log in of the ProGet Service to the System user (right click on the ProGet Service > Properties > Log On tab).

Start the ProGet service.

## Configure the Inedo Service Messenger

The service messenenger is a component of ProGet that enables simple communications between web and service nodes. By default it uses a named pipe, but in a load-balanced configuration it needs to be configured to use TCP instead. The messenger is optional, but certain parts of ProGet's web interface may be slightly degraded without it. See the documentation on the [Service Messenger](/docs/proget/installation/installation-guide/service-messenger) for more information.

## Upgrading a Load Balanced ProGet Installation

Download the latest ProGet manual installer ([https://inedo.com/proget/versions](/proget/versions)) and on each web node:

*   Stop the ProGet IIS AppPool
*   Make a copy of the appSettings section from Web.config
*   Copy the contents of Proget-WebApp.zip to `C:\ProGet\WebApp`, overwriting the old files
*   Ensure the appSettings section is correct (Web.config may have been overwritten in the upgrade, so restore settings copied in previous step)

On _ProGet_Webnode1,_ only:

*   Stop the INEDOPROGETSVC Windows Service
*   Copy the contents of Proget-Service.zip to `C:\ProGet\Service`

With all AppPools and services stopped, run the ProGet SqlScripts as described in the [manual install guide](/docs/proget/installation/installation-guide/manual)

After the database is updated:

*   Start the ProGet IIS AppPool on each web node
*   Start the INEDOPROGETSVC Windows Service on _ProGet_Webnode1_