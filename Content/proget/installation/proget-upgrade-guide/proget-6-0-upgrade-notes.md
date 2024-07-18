---
title: "Upgrading to ProGet 6.0 (2021)"
order: 4
url-slug: "proget-6-0-upgrade-notes"
---

:::(Error) (ProGet 6.0 Upgrade Not Recommended)
If you're currently using ProGet 5.2 or ProGet 5.3, we do not recommend upgrading to ProGet 6.0. 

Instead, **directly upgrade to ProGet 2024** ([see notes](/docs/proget-upgrade-2024)). This article is kept as-is to help show what changed in ProGet 6.0.
:::

ProGet 6.0 is a major upgrade, and this article provides information about what changed, the impact to your instance, and how to mitigate risk during upgrade.

## Planning for Your Upgrade

ProGet 6.0 is a major upgrade, and the majority of changes from ProGet 5.3 were  additive features, platform updates (library upgrades, etc.), and UI changes. 

:::(Warning) (Potential Bugs/Regressions: API Keys & Management)
The API Keys changes involved changing some of the authentication code; this may lead to bugs/regressions where a connected systems (build/CI server) reports authentication errors. You should be prepared to...

1. Edit/fix the appropriate [API Key](/docs/proget/reference-api/proget-apikeys) and adjust permissions
2. Or, rollback to the latest 5.3 if it doesn't work still

In either case, please [contact support](https://inedo.com/support) with issues so we can investigate and fix them in a maintenance release.
:::

API Keys aside, ProGet 6.0 is a relatively simple upgrade depending on which version you're upgrading from, and your usage of that version.

### Upgrading from ProGet 5.3
:::(Info)
If you're upgrading from ProGet 5.3, upgrading to 6.0 should be relatively easy.
:::

No major features were removed or changed, and the upgrade should be relatively easy, and no preparation will be needed. You can even rollback to ProGet 5.3 without rolling back the database. 

### Upgrading from ProGet 5.2
:::(Warning)
If you're upgrading from ProGet 5.2, you will need convert all "quirks" feeds before upgrading. Rolling back will also require restoring your database.
:::

While we recommend upgrading *directly* to ProGet 6.0 from ProGet 5.2 (and skipping 5.3), there were some substantial changes to the database made in ProGet 5.3. Make sure to read the [Upgrading to ProGet 5.3](/docs/proget-5-3-upgrade-notes) notes to learn what changed.

### Upgrading from ProGet 5.1 or Earlier
:::(Error)
If you're upgrading from ProGet 5.1 or earlier, you should upgrade to ProGet 5.2 first.
:::

We recommend upgrade to latest ProGet 5.2, then latest ProGet 5.3. See: [v5 notes](/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrading-to-proget-5-2), [ v4 notes](/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrade-notes-for-proget-v4), [v2/v3 notes](/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrading-from-proget-v2-and-v3)

## Changes in ProGet 6.0

### New Feature:  Asset Directory Improvements

We've added several new features to Asset Directories, including:
 * Support for Asset Directory Replication
 * Custom Client-side Caching
 * Set Custom Metadata/Headers
 * Configure cloud-based storage
 * Asset versioning/history

### New Feature: Feed Groups
These allow you to organize feeds in the user interface and create permissions and access controls that apply to an entire group of feeds.

### New Feature: Customizable Feed/Package Usage Instructions
Whether you use a specialized tool with your packages or have a different process to access feeds, customizing the built-in feed and package usage instructions will save a lot of headaches and help end users figure out how to use feeds and packages faster

### New Feature: Personal & Feed API Keys
Personal keys were already available as a preview feature (from v5.3.32) - administrators can enable Personal API Keys, which lets users create/delete API keys that are tied to their username.

### New Feature: User-selectable Dark Mode
Like with our other products, ProGet has have Dark Mode that each user can configure.

## Upgrade Process

You can perform the upgrade from the Inedo Hub.  The traditional (legacy) installer is no longer supported.

## Risk Mitigation

Compared with prior major versions, this is a relatively low-risk upgrade. Like with all upgrades, you should make sure your database is backed up.

There were no major changes the Feed APIs, so your integrations should still work just fine. There may be some bugs in the web UI due to the internal framework upgrades.

## Rolling Back

You can rollback using the Inedo Hub. 

:::(Info)
**You can rollback to any version of 5.3 without restoring the database.**
:::

While there are database schema changes, they are all backwards-compatible, which means you can safely rollback your ProGet installation if there's a showstopper bug, and then upgrade later.
.
