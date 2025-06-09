---
title: "Otter Installation Guide"
order: 1
---

Otter is self-managed, which means you have the option to run Otter on-premise or in your private/public cloud. Both Windows and Linux are supported.

## Installing on Windows Servers

Otter doesn't have any intense server requirements, and supports all modern version of Windows that Microsoft supports (see [Windows Lifecycle Fact Sheet](https://support.microsoft.com/en-us/help/13853/windows-lifecycle-fact-sheet){target="_blank"}). 

The recommended Windows server requirements are:

* Minimum - 2 Core CPU, 2 GB RAM, 1 GB for storage
* Recommended Average/Typical - 2 Core CPU, 4 GB RAM, 10 GB storage
* Recommended Large - 4 Core CPU, 8 GB RAM, 16 GB Storage

There are three supported options; there is also [Legacy (Traditional) Installer](/docs/installation/windows/installation-legacy-traditional-installer) available for Otter v2.1 and earlier.

### Easiest: Inedo Hub
The easiest way to install Otter on your Windows Servers is with [Inedo Hub](/docs/installation/windows/desktophub-overview), which is a lightweight Windows application. The Inedo Hub is used to install, update, and otherwise service your Otter installation, as well as other Inedo products installed on your server.

<a href="/docs/installation/windows/inedo-hub-installation-guide" style=" background:#025291;color:#ffffff;padding: 6px 20px;  border-radius: 3px;font-size: 14pt;text-decoration:none">Install Otter using the Inedo Hub</a>

The Inedo Hub also supports [Offline Installation](/docs/installation/windows/desktophub-overview/desktophub-offline) for severs with limited or no Internet access.

### Silent Installation for Automation
You can also perform a silent (non-interactive) installation of Otter using the Inedo Hub. This won't display a user interface or ask any questions. This can be useful for scripts or other automations.

See: [Silent/Automated Installation Guide](/docs/installation/windows/desktophub-overview/silent)

### Fully Customizable: Manual Installation
If you prefer to install every component of Otter yourself, that's also an option. 

See: [Manual installation](/docs/installation/manual-installation)


## Installing on Linux Servers

Otter can run on a Linux server through Docker. We may support additional installation options in the future if there's any interest.

The recommended Container resources are:

* Minimum - 2 vCPU, 2 GB RAM
* Recommended Average/Typical - 2 vCPU, 4 GB RAM
* Recommended Large - v4 CPU, 8 GB RAM

In addition, we recommend reserving a volume with at least 10GB to store your packages and container images.

<a href="/docs/installation/linux/docker-guide" style=" background:#025291;color:#ffffff;padding: 6px 20px;  border-radius: 3px;font-size: 14pt;text-decoration:none">Install Otter on Linux using Docker</a>

In addition to Docker Engine, you can run Otter through [Docker Compose](/docs/installation/linux/docker-compose-installation-guide) or Kubernetes.

## Running in the Cloud

Otter can be installed on a virtual machine or container in the cloud provider of your choosing. 

The [Installing ProGet on AWS Lightsail](/docs/proget/installation/proget-how-to-install-on-aws-lightsail) guide is a good starting point for using Lightsail to install an Inedo product.

## Firewall & Network Access
On Windows, Otter installs on port 8626 by default, and you should consider changing that after installation to the standard ports of HTTP (80) and/or HTTPS (443) after installation.

Whatever port you decide to access Otter's web application on, you'll need to configure an Inbound Firewall rule to allow that access. No other inbound ports are required.

### Server & Inedo Agent Network Access

Otter is used to orchestrate and deploy files to servers, and will need the appropriate network access (inbound or outbound) to communicate with the servers. This is all configurable, and whatever access is needed depends on how you configured your servers, and Otter to access those servers.


## Maintaining your Otter Installation

Once your Otter instance is up and running, there isn't a whole lot that you will need to manage or maintain. 
 
* **Update Otter at least quarterly**; we ship a maintenance release of Otter at least twice a month with bugfixes and low-risk changes. However, lots of small, low-risk changes add up over time, so we recommend updating at least once per quarter to minimize the risk of a regression. View our [Detailed Change Log](/docs/installation/upgrading#viewing-change-logs) and [Upgrade Guidance](/docs/installation/upgrading#viewing-upgrade-guidance) on MyInedo to learn more.

* **Routinely [back-up your instance](/docs/installation/backing-up-restoring)** to mitigate the risk of sudden hardware/server problems, as well as to make rolling back to previous versions of Otter easy.
