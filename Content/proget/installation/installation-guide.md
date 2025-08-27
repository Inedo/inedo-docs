---
title: "ProGet Installation Guide"
order: 1
---

ProGet is self-managed, meaning you have the option to run ProGet on-premises or in your private/public cloud. Both [Windows and Linux](https://inedo.com/proget/download) are supported.

## Installing on Windows Servers

ProGet doesn't have any intense server requirements, and supports all modern version of Windows that Microsoft supports (see [Windows Lifecycle Fact Sheet](https://support.microsoft.com/en-us/help/13853/windows-lifecycle-fact-sheet){target="_blank"}). 

The recommended Windows server requirements are:

* Minimum - 2 Core CPU, 4 GB RAM
* Recommended Average/Typical - 2 Core CPU, 8 GB RAM
* Recommended Large - 4 Core CPU, 16 GB RAM

ProGet itself does not require much disk space (less than 1GB); however, the content (package, container images, and asset files) you store in ProGet will determine how much space you'll need. We recommend reserving at least 10GB to store this content at first and be prepared to expand it.

There are two supported options; there is also [Legacy (Traditional) Installer (github.com)](https://github.com/Inedo/inedo-docs/blob/c82fd2881e2f1d0c36e77bc8b8b48e2a2c7b75a9/Content/installation/windows/installation-legacy-traditional-installer.md) available for ProGet v5.3 and earlier.

### Easiest: Inedo Hub
The easiest way to install ProGet on your Windows servers is to use [Inedo Hub](/docs/installation/windows/inedo-hub), a lightweight Windows application. Inedo Hub is used to install, update and maintain your ProGet installation as well as other Inedo products installed on your server.

<a href="/docs/installation/windows/howto-install" style=" background:#025291;color:#ffffff;padding: 6px 20px;  border-radius: 3px;font-size: 14pt;text-decoration:none">Install ProGet using the Inedo Hub</a>

The Inedo Hub also supports [Offline Installation](/docs/installation/windows/inedo-hub/offline) for servers with limited or no Internet access.

### Silent Installation for Automation
You can also perform a silent (non-interactive) installation of ProGet via Inedo Hub. In this case, no user interface is displayed and no questions are asked. This can be useful for scripts or other automations.

See: [Silent/Automated Installation Guide](/docs/installation/windows/inedo-hub/silent)

### Fully Customizable: Manual Installation
::: (WARNING) (Note)
We do not support manual installations, and this document is for informational and troubleshooting purposes only. If you really want to install the product without using Inedo Hub, you can follow these instructions—but your successor might have a very hard time maintaining this installation.
:::

If you prefer to install every component of ProGet yourself, that's also an option. 

See: [Manual installation](/docs/installation/windows/manual-installation)


## Installing on Linux Servers

ProGet can be run on a Linux server via Docker. We can support other installation options in the future if there is interest.

The recommended container resources are:

* Minimum - 2 vCPU, 2 GB RAM
* Recommended Average/Typical - 2 vCPU, 4 GB RAM
* Recommended Large - v4 CPU, 8 GB RAM

ProGet itself does not require much disk space (less than 1GB); however, the content (package, container images, and asset files) you store in ProGet will determine how much space you'll need. We recommend reserving a volume with at least  10GB to store this content at first and be prepared to expand it.

<a href="/docs/installation/linux/docker-guide" style=" background:#025291;color:#ffffff;padding: 6px 20px;  border-radius: 3px;font-size: 14pt;text-decoration:none">Install ProGet on Linux using Docker</a>

In addition to Docker Engine, you can run ProGet through [Docker Compose](/docs/installation/linux/docker-compose-installation-guide) or Kubernetes.

## Running in the Cloud

ProGet can be installed on a virtual machine or container in the cloud provider of your choice. 

See: [Installing ProGet on AWS Lightsail](https://inedo.com/proget/lightsail_install).

## Firewall & Network Access
On Windows, ProGet installs on port 8624 by default, and you should consider changing that after installation to the standard ports of HTTP (80) and/or HTTPS (443) after installation.

Whatever port you decide to access ProGet's web application on, you'll need to configure an Inbound Firewall rule to allow that access. No other inbound ports are required.

### Outbound Network Access
ProGet doesn't require Internet access. However, since ProGet is often used as a proxy for external repositories such as nuget.org, dockerhub.io, etc., outbound network access on web ports (80, 443) is required if you want to use this feature.


## Maintaining your ProGet Installation

Once your ProGet instance is up and running, there is not much you need to manage or maintain.
 
* **Update ProGet at least quarterly**; we deliver a maintenance release of ProGet at least twice a month with bug fixes and low-risk changes. However, many small, low-risk changes add up over time, so we recommend updating at least once per quarter to minimize the risk of regression. View our [Detailed Change Log](/docs/installation/upgrading#viewing-change-logs) and [Upgrade Guidance](/docs/installation/upgrading#viewing-upgrade-guidance) on MyInedo to learn more.

* **Routinely [back-up your instance](/docs/installation/backing-up-restoring)** to mitigate the risk of sudden hardware/server issues and to allow easy rollback to previous versions of ProGet.

* **Configure [Automatic Retention Rules](/docs/proget/administration/retention-rules)** so that unused packages and container images are automatically cleaned up.
