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

Individual changes are carefully documented on our <a href="https://issues.inedo.com/">public issue tracker</a> and follow our rigorous <a href="https://issues.inedo.com/youtrack/dashboard?id=fa2b918f-c687-4c96-a6ec-b9fa7e1c5c90">Change Management Process</a>. Users may even request <a href="https://inedo.com/open/source-code-request">source code access</a> to see exactly what changed.

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

### Upgrade Notifications

Your Inedo product will notify you of new version releases, but if you're using an offline version then you will not be notified of the latest version releases. This means that bug fixes and important security patches may go unnoticed.

Instead, visit your MyInedo page and select "Upgrade Guidance & Change Notes" in the [Downloads](https://my.inedo.com/downloads) tab or visit our [Public Isue Tracker](https://issues.inedo.com/dashboard?id=87c77108-8027-4453-aa65-15e83cf8782e) to view information on new releases and fixes.

![Upgrade_Guidance_Changle_Notes](/resources/docs/myinedo-viewingupgradeguidance-upgradeguidanceandchangenotes.png){height="" width="50%"}
We recommend visiting your MyInedo page regularly and viewing change logs to check if in-software update notifications bring changes applicable to your use case, and to make sure that your software is secure and optimized.

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

## Viewing Upgrade Guidance
If you're having difficulties upgrading your Inedo product, or don't know where to start, you can view upgrade guidance on [MyInedo](https://my.inedo.com/log-in), to determine the recommended route to your intended product version, and the risks involved. This is useful when upgrading from an older product version where a direct upgrade is not possible.

### How to View Upgrade Guidance

On your MyInedo page, choose your Inedo product, current version, and chosen upgrade version, then select "Get Upgrade Guidance".

![Upgrade_Guidance](/resources/docs/myinedo-viewingupgradeguidance-getupgradeguidance.png){height="" width="50%"}

### Recommended Upgrade Path

You can view our recommended steps for upgrading, specific to your version transition. This provides an assessment of risk, and links to Inedo Documentation pages with further instructions on upgrading based on your situation.

Upgrading can be a straightforward process, such as moving from `ProGet version 2024.25` to `version 2024.9`:

![Upgrade_Recommended](/resources/docs/myinedo-viewingupgradeguidance-upgraderecommended.png){height="" width="50%"}

In this case, follow the steps on the [Upgrading your Inedo Product](/docs/installation/upgrading) page to upgrade.

In some cases, multiple steps may be required. Refer to our guidance for detailed instructions:

![Upgrade_Not_Possible](/resources/docs/myinedo-viewingupgradeguidance-directupgradenotpossible.png){height="" width="50%"}

In this case, the first step requires upgrading to a different version of ProGet. Visit the [Upgrading to ProGet 5.2](/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrading-to-proget-5-2) page for detailed instructions on upgrading.

We may recommend upgrading to the latest version, for security and stability reasons, particularly when transitioning between two older versions of software:

![Upgrade_Not_Recommended](/resources/docs/myinedo-viewingupgradeguidance-upgradenotrecommended.png){height="" width="50%"}
Select **See Guidance** for detailed Inedo Documentation pages explaining how to upgrade, notable improvements and changes, and common issues / risks encountered.
### Change Notes
Despite a risk assessment being provided alongside your recommended upgrade path, it's good practice to note the changes in each maintenance release up to your intended version, visible on the same page.

![Change_Notes](/resources/docs/myinedo-viewingupgradeguidance-changelogsredbox.png){height="" width="50%"}
Words such as **fix** can help you pinpoint changes that could resolve issues you might be experiencing. **Add** and similar words help you spot changes that may improve your workflow.

Use these change notes to determine if there are any fixes, implemented features, or compatibility issues, that could result in a rollback.

## Viewing Change Logs
When you want to view change notes in detail to identify where an issue is stemming from after an upgrade or to search for the version in which a patch or feature you were anticipating was released, you can view our detailed change logs. The easiest way to do so is by clicking "Explore Detailed Change Logs". This lets you view the history of changes across all Inedo products.

![Explore Detailed Change Logs](/resources/docs/myinedo-viewingupgradeguidance-exploredetailedchangelogs.png){height="" width="50%"}

You can filter the change logs by count, product, and version to quickly locate changes in your product.

![Detailed Change Log Filters](/resources/docs/myinedo-viewingchangelogs-detailedchangelogfilters.png){height="" width="50%"}

### Using our Public Issue Tracker to View Details
Each update links to our [Public Issue Tracker](https://issues.inedo.com/dashboard?id=87c77108-8027-4453-aa65-15e83cf8782e&_gl=1*m68oqx*_gcl_au*NTUyMjMzOTM0LjE3NDE1ODEzMzM.) with more detailed information on the change.

![YouTrack](/resources/docs/myinedo-viewingchangelogs-youtrack.png){height="" width="50%"}

You can view the complete timeline of the issue, including when it started, when it was resolved, and the release version it was addressed.

If the log shows the state as "completed", this lets you know the change has been implemented in a particular release.
