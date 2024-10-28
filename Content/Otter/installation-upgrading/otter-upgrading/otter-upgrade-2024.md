---
title: "Upgrading to Otter 2024"
order: 1
url-slug: "otter-upgrade-2024"
---

Otter 2024 is a major upgrade, and this article provides information about what changed, the impact to your instance, and how to mitigate risk during upgrade.

## Planning for Your Upgrade

Although Otter 2024 is a major upgrade, the majority of changes were platform updates (library upgrades, etc.). 

No major features have been removed and there were no significant changes to the core components; we anticipate the upgrade will be relatively easy and no preparation will be required. 

:::(Warning) (Upgrading from Otter 3.0 or Earlier?)
Make sure to read [what changed in Otter 2022](/docs/otter-upgrade-2022) so you can learn how it may impact your installation.
:::

You can safely downgrade to Otter 2023 without rolling back the database.

## Changes in Otter 2024

The majority of changes were platform updates (library upgrades, etc.), but there were two notable changes:

* **Insecure Git Rafts** can now be added/used in Otter when you bypass certificate errors on the connection
* **HTTPS/SSL Settings (Integrated Web Server)** can now be edited from the Administration section


### SDK Changes
Otter 2024 targets `Inedo.SDK-3.0` and .NET8, and you'll need to rebuild any custom extensions you've created. See [creating an extension documentation](/docs/inedosdk/extending-inedo-tools-using-the-sdk/inedosdk-extending-creating) to learn more about how to do this.


## Upgrade Process
You can perform the upgrade from the Inedo Hub. Like with all upgrades, you should make sure your [installation is backed-up](/docs/installation/backing-up-restoring).

You can upgrade from any version of Otter to Otter 2024, and there is no need to install intermediate versions. But do note that you can only rollback to Otter 2023; any earlier, and you'll need to restore your database as well.


## Rolling Back

You can rollback using the Inedo Hub.

:::(Info)
**You can rollback to any version of Otter 2023 without restoring the database.**
:::

<!--
While there are database schema changes, they are all backwards-compatible, which means you can safely rollback your Otter installation if there's a showstopper bug, and then upgrade later.
-->
