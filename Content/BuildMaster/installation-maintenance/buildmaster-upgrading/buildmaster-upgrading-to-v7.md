---
title: "Upgrading to BuildMaster 7.0"
nav-title: "BuildMaster 7.0 (2021)"
order: 5
url-slug: "buildmaster-upgrading-to-v7"
---

:::(Error) (Skip BuildMaster 7.0: Upgrade Directly to BuildMaster 2025)
If you are using BuildMaster 6.1 or BuildMaster 6.2, we recommend directly [Upgrading to BuildMaster 2025](/docs/buildmaster-upgrade-2025). If you using an earlier version, you will first need to [Upgrade to BuildMaster 6.1](/docs/buildmaster-upgrading-to-6-1).
:::
BuildMaster 7.0 is a major upgrade. This article contains information about the changes, the impact on your instance, and how to minimize risk during the upgrade.

Upgrading from BuildMaster 6.1 or BuildMaster 6.2 to BuildMaster 7.0 is no longer recommended, and this article is for informational purposes only.

## Planning for Your Upgrade

Although BuildMaster 7.0 is a major upgrade, the changes from BuildMaster 6.2 were mainly platform updates (library upgrades, etc.) and UI changes. This means that it can be a relatively simple upgrade, depending on which version you're upgrading from and how you use that version.
  
### Upgrading from BuildMaster 6.2
:::(Info)
If you're upgrading from BuildMaster 6.2, upgrading to 7.0 should be relatively easy.
:::

No major features have been removed or changed, and the upgrade should be relatively easy, with no preparation required. You can even rollback to BuildMaster 6.2 without rolling back the database.

### Upgrading from BuildMaster 6.1 or Earlier
:::(Warning)
If you're upgrading from BuildMaster 6.1 or earlier, upgrading to 7.0 may be challenging.
:::
In BuildMaster 6.2, all [Legacy Features](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features) were removed from the product. See [Upgrading to BuildMaster 6.2](/docs/buildmaster-upgrading-to-6-2) to learn more about what else changed in that release. Alternatively, you may want to consider an [application-by-application migration approach](/docs/buildmaster/installation-maintenance/buildmaster-migrating-instance-to-new-server#migrating-applicationbyapplication).

You wont be able to upgrade to BuildMaster 7.0 until the [Legacy Feature Detector](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features#legacy-feature-detector) has reported no legacy features. This is only available in the latest versions of BuildMaster 6.1, which means you will first need to [upgrade to BuildMaster 6.1](/docs/buildmaster-upgrading-to-6-1).

Because upgrading from BuildMaster 6.1 is more involved than previous upgrades, special support for it has been included with paid licenses. Simply use the [Submit Ticket Form](https://my.inedo.com/tickets/new), put `7.0 UPGRADE` in the "How can we help" section, and include the [Legacy Features Dashboard Report Logs](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features#legacy-feature-detector) in the text body of the ticket.

## Changes in BuildMaster 7.0

### New Feature: Style Updates & Dark Mode Option in UI

While the UI has not changed too much overall, we have completely overhauled the stylesheets behind the scenes. This may result in buttons being a different color throughout the software. This change has allowed us to introduce a new feature (Dark Mode) that uses a darker background to make it easier on the eyes. This mode is brand new to us and is under constant development; let us know how we can improve it.

### New Feature: Release Templates Overhaul

We have made some important improvements to the way you manage and use release templates. In general, we recommend favoring release templates so that you can get variable prompts and use consistent pipelines.

This is almost entirely a change from UI, which will not affect your existing data or release templates. Instead, you will notice a streamlined release template editor and creating releases from templates will be much easier.

### New Feature: Outbound-communication Mode for Inedo Agents

The Inedo Agent can now operate in an outbound mode, which means that BuildMaster v7 can accept incoming connections from agents instead of contacting agents directly. This opens up new possibilities for cloud hosting of BuildMaster while providing access to on-premises resources.

### New Feature: Run as Docker Container on Linux

BuildMaster is now cross-platform! You can now run BuildMaster as a Linux container instead of hosting on a Windows server. See our [Linux and Docker Installation Guide](/docs/installation/linux/docker-guide).

### New Feature: High-availability & Load Balancing

BuildMaster can now be run as a cluster, in [high-availability and load-balanced mode](/docs/installation/high-availability-load-balancing/high-availability-load-balancing). This not only distributes executions across multiple servers (which in turn communicate with remote servers via agents), but also ensures that if a single server in a BuildMaster cluster fails, the application continues to run largely uninterrupted.

### Deprecated Feature: Deployables

Deployables have been a vestigial feature since v5 and have been hidden from new users of UI since v6. As of v7 they are officially deprecated, which means we recommend migrating away from them if you use them.

As part of the internal updates to the web framework and changes to the release templates, we have removed some functionality from UI, such as providing error messages if you do not edit deployables correctly. We doubt anyone will notice this, but let us know if there are any issues.

### Removed: Environment Soft Delete

Environments are no longer soft deleted (i.e., hidden but marked as deleted in the database); this behavior has been removed to simplify the code, reduce errors, and better meet user expectations for deletion.

When upgrading to v7, your soft deleted environments (that you probably didn't even know were still there) will be permanently deleted. The database column is retained for compatibility and allows you to downgrade to 6.2 if needed.

## Upgrade Process

You can upgrade via the Inedo Hub.

If you are upgrading from BuildMaster 6.1, you must first upgrade to the latest version of BuildMaster 6.1. After installation, you must also upgrade the extensions. If you were using the `SqlServer 1.0.0` extension, it will not load; delete it and download the latest SQL Server extension.

## Risk Mitigation

Compared to previous major versions, this is a relatively low-risk upgrade. As with all upgrades, you should ensure that your database is backed up.

There have been no major changes to the execution engine, pipelines, or deployment plan logic, so your applications should continue to build and deploy smoothly. Due to the internal framework upgrades, you may experience some errors in the web UI.

It is not _necessary_ to upgrade extensions, but you will be prompted to do so. Before upgrading extensions, make sure you back up your installation's `Extensions` directory (`Extensions.ExtensionsPath` in Advanced Settings) so you can roll back if needed. This will save you the hassle of a downgrade later.

## Rolling Back

You can perform a rollback using the Inedo Hub. If you use the traditional installer, you'll need to uninstall the older version of BuildMaster and then reinstall it.

:::(Info)
**You can roll back to any version of 6.2 without restoring the database.**

If you upgrade to any version of v7 from v6.2, then you can downgrade to your previous 6.2 version without a database restore.
:::

Although there are changes to the database schema, they are all backward compatible, which means that you can safely reset your BuildMaster installation if an error occurs, and then upgrade later.

**You may also need to rollback to old extension versions.** BuildMaster v7 uses the Inedo SDK v1.12, which cannot be used by BuildMaster v6.2.