---
title: ProGet & Microsoft NLB Guide
sequence: 400
show-headings-in-nav: true
keywords: proget, installation, load-balanced
---

## Overview

This example guide is designed to configure a cluster and its various web servers in a Network Load Balancing (NLB) environment in order to enable the load balancing feature in ProGet. This example guide uses [Microsoft Windows NLB feature](https://docs.microsoft.com/en-us/windows-server/networking/technologies/network-load-balancing) as a load balancer, and all machines in this cluster are VMs, though that is not a strict requirement.

This guide is intended to help you set up your load-balanced or High Availability (HA) installation. ProGet was built to be compatible with any load-balancing platform (see [load balancing and automatic failover](/docs/proget/installation/load-balancing-and-automatic-failover) for more information).

## Prerequisites {#prerequisites data-title="Prerequisites"}

To begin, you must meet certain prerequisites, which are:

{.docs}
 - ProGet Enterprise license
 - ProGet already installed and running on at least one server (see [Load Balancing & Automatic Failover](/docs/proget/installation/load-balancing-and-automatic-failover))
 - Refer to the [Installation Checklist](/docs/proget/installation/installation-guide#pre-install )

### Microsoft NLB Prerequisites

For Microsoft NLB installs, the following are additional prerequisites should be considered:

{.docs}
 - 2 network interfaces (NICs) total (one dedicated for the NLB cluster), each with static IPs to host the ProGet website 
    - Three machines to install ProGet *(example machine names in italics)*
        - _ProGetSv1_ (already has ProGet installed)
        - _ProGetSv2_
        - _ProGetSv3_
    - Machine to set up the NLB cluster *(example machine names in italics)*
        - _ProGetNLB_
 - Cluster IP with subnet mask (see [Configuring the Cluster](#cluster-configuration) below) mapped to the internet name and company domain (e.g. `http://proget.kramerica.local/`)
 - Address Resolution Protocol (ARP) entry using the cluster IP to the switch that routes into the NLB VLAN

### Proget Server Prerequisites

ProGet load balancing requires IIS. The required settings are outlined in [IIS Roles & Features](/docs/various/iis/roles-and-features). You also have to configure additional features in the Server Manager by going to Features > Network Load Balancing in your ProGet instance.

## Configuring the Cluster {#cluster-configuration data-title="Configuring the Cluster"}

To configure the cluster, follow these steps:
{.docs}
 - Connect to _ProGet_NLB_ as the System user using a remote desktop connection
 - Open the NLB manager (Control Panel\All Control Panel Items\Administrative Tools\Network Load Balancing Manager)
    - Create a new cluster
    - Use the following as a guide to setup NLB: Microsoft's [Network Load Balancing Cluster](https://technet.microsoft.com/en-us/library/cc771008.aspx).
**Note: Session Affinity (sticky sessions) is *not* required.**

### Cluster Properties Example

1. **IP** - provided by IT, e.g. 10.0.0.100
2. **Subnet mask** - provided by IT, e.g. 255.255.250.0
3. **Internet name** - provided by IT, e.g. proget.kramerica.local
4. **Operation mode** - Multicast
5. **Port range** - 80 to 80
6. **Protocols** - TCP
7. **Filtering mode** - Multiple host\Single | None (None option preferred)
    - None (preferred); indicates that any host in the cluster which matches to the port rule can handle the client request
    - Multiple host\Single; indicates that one host in the NLB cluster can process traffic from the same client. The requests are basically transmitted to the same host every time NLB reads the IP address.

## Configuring the ProGet Servers {#node-configuration data-title="Configuring the ProGet Servers"}

Follow the [ProGet Website & ProGet Service configuration](/docs/proget/installation/load-balancing-and-automatic-failover#web-node) for installing each server and, optionally, the [High Availability configuration](/docs/proget/installation/load-balancing-and-automatic-failover#ha) to configure HA. 

The following additional IIS configuration should be considered:

 - Use the bindings as per the Cluster Properties:
   - Cluster IP - provided by IT (e.g. `10.0.0.100`).
   - Port - 80
   - Host name - provided by IT (e.g. `proget.kramerica.local`)
