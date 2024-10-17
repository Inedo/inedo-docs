---
title: "BuildMaster Installation Guide"
order: 1
---

BuildMaster is self-managed, which means you have the option to run BuildMaster on-premises or in your private/public cloud. Both Windows and Linux are supported.

## Installing on Windows Servers

BuildMaster doesn't have any intense server requirements, and supports all modern version of Windows that Microsoft supports (see [Windows Lifecycle Fact Sheet](https://support.microsoft.com/en-us/help/13853/windows-lifecycle-fact-sheet){target="_blank"}). 

The recommended Windows server requirements are:

* Minimum - 2 Core CPU, 4 GB RAM
* Recommended Average/Typical - 2 Core CPU, 8 GB RAM
* Recommended Large - 4 Core CPU, 16 GB RAM

BuildMaster itself does not require much disk space (less than 1GB); however, two major factors will impact how much space you'll need:
* the build artifacts you store in BuildMaster 
* the size of the Git repositories you've sync'd to BuildMaster

We recommend reserving at least 10GB to store this content at first and be prepared to expand it.

There are three supported options; there is also [Legacy (Traditional) Installer](/docs/installation/windows/installation-legacy-traditional-installer) available for BuildMaster v6.2 and earlier.

### Easiest: Inedo Hub
The easiest way to install BuildMaster on your Windows Servers is with [Inedo Hub](/docs/installation/windows/desktophub-overview), which is a lightweight Windows application. The Inedo Hub is used to install, update, and otherwise service your BuildMaster installation, as well as other Inedo products installed on your server.

<a href="/docs/installation/windows/inedo-hub-installation-guide" style=" background:#025291;color:#ffffff;padding: 6px 20px;  border-radius: 3px;font-size: 14pt;text-decoration:none">Install BuildMaster using the Inedo Hub</a>

The Inedo Hub also supports [Offline Installation](/docs/installation/windows/desktophub-offline) for severs with limited or no Internet access.

### Silent Installation for Automation
You can also perform a silent (non-interactive) installation of BuildMaster via Inedo Hub. In this case, no user interface is displayed and no questions are asked. This can be useful for scripts or other automations.

See: [Silent/Automated Installation Guide](/docs/installation/windows/silent)

### Fully Customizable: Manual Installation
If you prefer to install BuildMaster yourself, that's also an option. 

See: [Manual installation](/docs/installation/manual-installation)


## Installing on Linux Servers

BuildMaster can run on a Linux server via Docker. We may support additional installation options in the future, if there's any interest.

The recommended Container resources are:

* Minimum - 2 vCPU, 2 GB RAM
* Recommended Average/Typical - 2 vCPU, 4 GB RAM
* Recommended Large - v4 CPU, 8 GB RAM

In addition, we recommend reserving a volume with at least 10GB to store your packages and container images.

<a href="/docs/installation/linux/docker-guide" style=" background:#025291;color:#ffffff;padding: 6px 20px;  border-radius: 3px;font-size: 14pt;text-decoration:none">Install BuildMaster on Linux using Docker</a>

In addition to Docker Engine, you can run BuildMaster through [Docker Compose](/docs/installation/linux/docker-compose-installation-guide) or Kubernetes.

## Running in the Cloud

BuildMaster can be installed on a virtual machine or container in the cloud provider of your choosing. 

The [Installing ProGet on AWS Lightsail](https://inedo.com/proget/lightsail_install) guide is a good starting point for using Lightsail to install an Inedo product.

## Firewall & Network Access
On Windows, BuildMaster is installed on port 8622 by default, and you should consider changing this to the default HTTP (80) and/or HTTPS (443) ports after installation.

Regardless of which port you use to access the BuildMaster web application, you must configure an inbound firewall rule to allow this access. Other inbound ports aren't required.

### Server & Inedo Agent Network Access

BuildMaster is used to orchestrate and deploy files to servers, and requires the appropriate network access (inbound or outbound) to communicate with the servers. This is all configurable, and which access is required depends on how you have configured your servers and BuildMaster to access those servers.


## Maintaining your BuildMaster Installation

Once your BuildMaster instance is up and running, there isn't much you will need to manage or maintain. 
 
* **[Update BuildMaster](/docs/buildmaster-upgrading) at least quarterly**; we deliver a maintenance release of BuildMaster at least twice a month with low-risk bug fixes and changes. However, many small, low-risk changes add up over time, so we recommend updating BuildMaster at least once per quarter to minimize the risk of regression.

* **Routinely [back-up your instance](/docs/installation/backing-up-restoring)** to mitigate the risk of sudden hardware/server issues and to facilitate rollback to previous BuildMaster versions.
