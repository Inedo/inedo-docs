---
title: ProGet & Microsoft NLB Guide
sequence: 400
show-headings-in-nav: true
keywords: proget, installation, load-balanced
---

## Overview

This example guide is designed to configure a cluster and its various web servers in a Network Load Balancing (NLB) environment, in order to enable the Load Balancing feature in ProGet. This uses [Microsoft Windows NLB feature](https://docs.microsoft.com/en-us/windows-server/networking/technologies/network-load-balancing) as a load balancer, and all machines in this cluster are VMs, though that is not a strict requirement.

This guide is intended to help you set up your load-balanced or high-availability installation.  ProGet was built to be compatible with any load-balancing platform, see [load balancing and automatic failover](/docs/proget/installation/load-balancing-and-automatic-failover) for more information.

## Prerequisites {#prerequisites data-title="Prerequisites"}

 - ProGet Enterprise license
 - Running ProGet installation with at least one node (see [Load Balancing & Automatic Failover](/docs/proget/installation/load-balancing-and-automatic-failover))
 - Refer to the [Installation Checklist](/docs/proget/installation/installation-guide#pre-install )

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

 - Required settings outlined in [IIS Roles & Features](/docs/various/iis/roles-and-features)
 - Additional features configured in the Server Manager:
   - Features > Network Load Balancing

## Configuring the Cluster {#cluster-configuration data-title="Configuring the Cluster"}

 - RDP as the System user into _ProGet_NLB_+
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

## Configuring the ProGet Web Nodes {#node-configuration data-title="Configuring the ProGet Nodes"}

Follow the [web node & ProGet service configuration](/docs/proget/installation/load-balancing-and-automatic-failover#web-node) for installing each web node and, optionally, the [high availability configuration](/docs/proget/installation/load-balancing-and-automatic-failover#ha) to configure high availability. 

The following additional IIS configuration should be considered:

 - Use the bindings as per the Cluster Properties:
   - Cluster IP - provided by IT (e.g. `10.0.0.100`).
   - Port - 80
   - Host name - provided by IT (e.g. `nuget.srv.companyname.corp`)