---
title: "Upgrading your Inedo Product"
order: 9
---

ProGet, BuildMaster, and Otter will never automatically update themselves. You are in complete control of when and how often you upgrade, and you can just as easily downgrade if needed. This lets you eliminate the "surprise" from unwanted new features and drastically reduce the impact of changes or bugs (regressions).

Although it's really easy to upgrade your Inedo product, that doesn't mean you  should upgrade as soon as a new version is available. This article gives guidance on how and when you should upgrade.

:::(Info) (Looking for Product-specific Upgrade Guidance?)
We've put together a guide for each product that has the recommended upgrade paths for major versions.
* [ProGet Upgrade Guide](/docs/proget-upgrade-guide)
* [BuildMaster Upgrade Guide](/docs/buildmaster-upgrading)
* [Otter Upgrade Guide](/docs/otter-upgrading)
:::


## When should I upgrade my Inedo Product?

Before updating to a newer version, you should weigh the costs associated with the update process against the value and benefits of the new features or bug fixes added. To help you do this, we have two categories of releases.

### Maintenance Releases (e.g. 2022.14  to 2022.15)
::: (Info) (Best Practice: Upgrade at least Quarterly)
Maintenance releases are low risk and contain only backward-compatible enhancements, bug fixes, and security patches.
:::

We publish maintenance releases of each product at least twice a month, and most of the changes are very small and impact only a handful of customers. We will occasionally ship "preview" features in maintenance updates, but only if it can be done safely and if it's clearly marked in the product as a preview. 

Individual changes are carefully documented on our <a href="https://issues.inedo.com/">public issue tracker</a> and follow our rigorous <a href="https://issues.inedo.com/youtrack/dashboard?id=fa2b918f-c687-4c96-a6ec-b9fa7e1c5c90">Change Management Process</a>. Users may even request <a href="https://inedo.com/open/source-code-request">source code access</a> to see exactly what changed.</p>

However, lots of small, low-risk changes add up over time, so we recommend upgrading  at least once per quarter to minimize the risk of regression.

### Major Releases (e.g. 2022 to 2023, or 7.0 to 2022)
::: (Info) (Best Practice: Carefully Read Upgrade Notes)
A major release typically implements a brand new feature or overhauls an existing one, and is inherently riskier than a maintenance release.
:::

Each major release is a bit different, and may include:

* backward-incompatible API changes
* SDK changes that may require custom extensions to be recompiled
* major database schema changes
* major UX changes
* major functionality updates
* platform/system requirement changes
* all bug fixes up to and including the new major version

Because of the major changes that may be introduced, you should always carefully read the upgrade notes performing a major update. These can be found under the Installation & Maintenance section of each product, in the documentation.

In most cases, you should schedule a specific time slot away from crucial deployments in order to perform a major update. Many users even set up separate instances in order to explore functionality before rolling out the update to their production instance.

## How to Upgrade

Before upgrading your Inedo product, we recommend [performing a back-up](/docs/installation/backing-up-restoring), just in case something goes wrong and you need to rollback. This is especially important when performing a major upgrade.

Performing the upgrade itself is generally very easy.

### Upgrading on Windows Servers
The easiest way to manage your product installation on Windows is with [Inedo Hub](/docs/installation/windows/desktophub-overview). This is a lightweight Windows application that's used to install, update, and otherwise service the Inedo products on your server.

<a href="/docs/installation/windows/inedo-hub-upgrade-downgrade" style=" background:#025291;color:#ffffff;padding: 6px 20px;  border-radius: 3px;font-size: 14pt;text-decoration:none">HOWTO: Upgrade using the Inedo Hub</a>


### Upgrading on Linux Servers

Inedo products running on Linux using Docker containers, and can be upgraded by installing and running a new version of the container image.

<a href="/docs/installation/linux/installation-upgrading-docker-containers" style=" background:#025291;color:#ffffff;padding: 6px 20px;  border-radius: 3px;font-size: 14pt;text-decoration:none">Upgrading Docker Containers</a>

You can also run ProGet through [Docker Compose](/docs/installation/linux/docker-compose-installation-guide) or Kubernetes, which will follow the same upgrade principles.
