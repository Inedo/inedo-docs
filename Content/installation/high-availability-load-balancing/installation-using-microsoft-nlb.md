---
title: "Using Microsoft Network Load Balancing (NLB)"
order: 3
---

This example guide is designed to configure a cluster and its various web servers in a Network Load Balancing (NLB) environment in order to enable the load balancing feature in ProGet, Otter, and BuildMaster. This example guide uses [Microsoft Windows NLB feature](https://docs.microsoft.com/en-us/windows-server/networking/technologies/network-load-balancing) as a load balancer, and all machines in this cluster are VMs, though that is not a strict requirement.  You can manage your cluster by using the cluster management page.

:::(Error) (Microsoft Network Load Balancing (NLB) is No Longer Recommended)
While Microsoft still claims to support NLB, they are no longer adding features nor fixing bugs. Instead, Microsoft is focusing on [Software Load Balancer (SLB)](https://learn.microsoft.com/en-us/azure-stack/hci/concepts/software-load-balancer), which as part of their Azure-inspired [Software Defined Networking (SDN)](https://learn.microsoft.com/en-us/azure-stack/hci/concepts/software-defined-networking) infrastructure.

NLB also does not add `X-Forwarded*` headers, which you won't be able to identify the underlying IP addresses accessing the web applications.

:::

This guide is intended to help you set up your load-balanced or High Availability (HA) installation. ProGet, BuildMaster, and Otter were built to be compatible with any load-balancing platform (see [load balancing and automatic failover](/docs/installation/high-availability-load-balancing/high-availability-load-balancing) for more information).

## Prerequisites 

To begin, you must meet the prerequisites, which are:

 - An Enterprise license
 - ProGet, BuildMaster, or Otter already installed and running on at least one server (see [Load Balancing & Automatic Failover](/docs/installation/high-availability-load-balancing/high-availability-load-balancing))
 - Refer to the [Installation Checklist](/docs/installation/manual-installation#prerequisites)

### Microsoft NLB Prerequisites

For Microsoft NLB installs, the following are additional prerequisites that should be considered:

 - 2 network interfaces (NICs) total (one dedicated for the NLB cluster), each with static IPs to host the product website 
    - Three machines to install ProGet, BuildMaster, or Otter *(example machine names in italics)*
        - _InedoSv1_ (already has a product installed)
        - _InedoSv2_
        - _InedoSv3_
    - Machine to set up the NLB cluster *(example machine names in italics)*
        - _Inedo_NLB_
 - Cluster IP with subnet mask (see [Configuring the Cluster](#configuring-the-cluster) below) mapped to the internet name and company domain (e.g. `http://proget.kramerica.local/`)
 - Address Resolution Protocol (ARP) entry using the cluster IP to the switch that routes into the NLB VLAN

### Server Prerequisites

ProGet, Otter, and BuildMaster load balancing requires IIS. The required settings are outlined in [IIS Roles & Features](/docs/installation/installing-on-iis/various-iis-configuring-iis-roles-and-features). You also have to configure additional features in the Server Manager by going to Features > Network Load Balancing in your ProGet, BuildMaster, or Otter instance.

:::(Info) (NOTE)
Linux/Docker support is coming soon for Load Balancing.  Microsoft NLB does not support Linux, you will need to use a different load balancer for that configuration.
:::

## Configuring the Cluster

To configure the cluster, follow these steps:

 - Connect to _Inedo_NLB_ as the System user using a remote desktop connection
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

## Configuring the Servers 

Follow the [Website & Service configuration](/docs/installation/high-availability-load-balancing/high-availability-load-balancing) for installing each server and, optionally, the [High Availability configuration](/docs/installation/high-availability-load-balancing/high-availability-load-balancing#configure-high-availability) to configure HA. 

The following additional IIS configuration should be considered:

 - Use the bindings as per the Cluster Properties:
   - Cluster IP - provided by IT (e.g. `10.0.0.100`).
   - Port - 80
   - Host name - provided by IT (e.g. `proget.kramerica.local`)