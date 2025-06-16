---
title: "Configuration Export, Import, & Sync"
order: 4
---

Otter allows you to share infrastructure configuration (servers, roles, environments, and variables) across instances of Otter and BuildMaster.

You can manually export/import this data, or automatically synchronize it across instances.

## Exporting Infrastructure Configuration
You can export all of your instance's infrastructure configuration ([servers](/docs/otter/connecting-to-your-servers-with-otter/otter-servers-in-otter), [environments](/docs/otter/connecting-to-your-servers-with-otter/otter-modeling-infrastructure-environments), and [roles](/docs/otter/connecting-to-your-servers-with-otter/otter-modeling-infrastructure-server-roles)) as a JSON file. Once exported, you can simply edit the file (adding or removing any number of infrastructure items) and import, share it with another instance of Otter or BuildMaster, or maintain it as a simple back-up.

![Otter-Export-Configuration](/resources/docs/export-config.png){height="" width=""}

## Importing Infrastructure Configuration
The import function allows you to quickly configure or make massive changes to Otter's infrastructure configuration by importing the configuration file that was exported from another instance of Otter or BuildMaster.

By using the "dry run" option you can see the changes that would be made before actively making the changes.

![import-config](/resources/docs/import-config.png){height="" width="50%"}

## Infrastructure Synchronization
It's quite common to use Otter to manage server configuration, and [BuildMaster](/docs/buildmaster/overview) for deploying applications to servers using CI/CD; because they both use the same set of servers, you can synchronize the infrastructure between multiple instances.

When you enable infrastructure synchronization, your Otter instance connects to either a BuildMaster or Otter instance, pulls configuration from that instance, and updates the infrastructure configuration as needed.

When synchronization is enabled, the other instance should be treated as the source of truth. There will also be limited options in the UI for editing configuration, as they would almost immediately be wiped-out when a synchronization occurs.

To have another instance of BuildMaster or Otter synchronize with your instance, simply create an API key with view infrastructure permissions.

![Otter Synchronization Settings](/resources/docs/otter-servers-syncsettings.png){height="" width="50%"}
