---
title: ProGet Load-balanced Installation
sequence: 400
show-headings-in-nav: true
keywords: proget, installation, load-balanced
---

::: {.attention .best-practice}
These step-by-step instructions were originally developed by Scott Cusson (Senior Release Engineer, Symbotic), primarily as a guide for internal use. We've modified and shared it as a reference guide that may help you set up your load-balanced or high-availability installation. See [load balancing and automatic failover](/docs/proget/installation/load-balancing-and-automatic-failover) for more information.
:::

## Overview

This example guide is designed to configure a cluster and its various web servers in a Network Load Balancing (NLB) environment, in order to enable the Load Balancing feature in ProGet. This uses [Microsoft Windows NLB feature](https://docs.microsoft.com/en-us/windows-server/networking/technologies/network-load-balancing) as a load balancer, and all machines in this cluster are VMs, though that is not a strict requirement.

## Prerequisites

 - ProGet Enterprise license
 - Running ProGet installation with at least one node (see [Manual Installation Instructions](/docs/proget/installation/installation-guide/manual#first-run))
 - Refer to the [Manual Installation Checklist](/docs/proget/installation/installation-guide/manual#checklist)

### NLB Prerequisites

For NLB installs, the following are additional prerequisites should be considered:

 - 2 NICs total (one dedicated for the NLB cluster) each with static IPs to host the ProGet website *(example machine names in italics)*
    - One machine per web node
        - _ProGet_Webnode1_ (this will also serve as the indexing server)
        - _ProGet_Webnode2_
        - _ProGet_Webnode3_
    - Machine to set up the NLB cluster
        - _ProGet_NLB_
 - Cluster IP with subnet mask (see [Configuring the Cluster](#cluster-configuration) below) mapped to the internet name and company domain (e.g. `http://nuget.srv.companyname.corp/`)
 - Address Resolution Protocol (ARP) entry using the cluster IP to the switch that routes into the NLB VLAN

### Web Node Prerequisites

 - Existing prerequisites under [Web Node Manual Install Instructions](/docs/proget/installation/installation-guide/manual#web)
 - Additional features configured in the Server Manager:
   - Features > Network Load Balancing
   - Features > .NET 4.5+ - (for IIS machine key gen, also install all options)

## Configuring the Cluster {#cluster-configuration}

 - RDP as the System user into _ProGet_NLB_
 - Open the NLB manager (Control Panel\All Control Panel Items\Administrative Tools\Network Load Balancing Manager)
    - Create a new cluster
    - Use the following as a guide to setup NLB: Microsoft's [Network Load Balancing Cluster](https://technet.microsoft.com/en-us/library/cc771008.aspx).
        - *Session Affinity (sticky sessions) is not required*

### Cluster Properties Example

1. **IP** - provided by IT. 10.0.0.100
2. **Subnet mask** - provided by IT. 255.255.250.0
3. **Internet name** - provided by IT. NUGET.SRV
4. **Operation mode** - Multicast
5. **Port range** - 80 to 80
6. **Protocols** - TCP
7. **Filtering mode** - Multiple host\Single | None (None option preferred)
    - None (preferred); indicates that any host in the cluster which matches to the port rule can handle the client request
    - Multiple host\Single; indicates that one host in the NLB cluster can process traffic from the same client. The requests are basically transmitted to the same host every time NLB reads the IP address.

## Configuring the ProGet Web Nodes

Follow the [Web Node Manual Instructions](/docs/proget/installation/installation-guide/manual#web) for each web node. The following additional configuration should be considered:

 - Use the bindings as per the Cluster Properties:
   - Cluster IP - provided by IT (e.g. `10.0.0.100`)
   - Port - 80
   - Host name - provided by IT (e.g. `nuget.srv.companyname.corp`)
 - In IIS Manager:
    - Select the ProGet website
    - Double click Machine Key
        - Use validation method SHA1 and Auto Encryption method
        - Deselect any check boxes and click Generate Keys and Apply on the right panel; this will update the web.config with a machine key

## Configure ProGet to Enable Load Balancing

On the ProGet Administration page, visit the "Configure Load Balancing" page, and click Enable.

## Configure ProGet Service Node

Visit the [Service Node Manual Install Instructions](/docs/proget/installation/installation-guide/manual#service) to install a service node.

For HA installations, it is recommended to install service nodes on separate machines from the web nodes, but there is no technical restriction on installing a web and service node on the same machine.

## Configure the Inedo Service Messenger

The service messenenger is a component of ProGet that enables simple communications between web and service nodes. By default it uses a named pipe, but in a load-balanced configuration it needs to be configured to use TCP instead. The messenger is optional, but certain parts of ProGet's web interface may be slightly degraded without it. See the documentation on the [Service Messenger](/docs/proget/installation/installation-guide/service-messenger) for more information.

## Upgrading a Load Balanced ProGet Installation

To upgrade an existing HA/LB installation, follow the [Upgrading a Manual Install Instructions](/docs/proget/installation/installation-guide/manual#upgrade) for each web and service node.
