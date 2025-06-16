---
title: "Configuration Export, Import & Sync"
order: 4
---

BuildMaster allows you to visualize the infrastructure your pipelines are targeting, and also gives you the ability to import and export infrastructure configuration. This makes sharing and maintaining your infrastructure configuration much easier.

## Exporting Infrastructure Configuration

You can export all of your instance's infrastructure configuration ([servers](/docs/buildmaster/administration-agents-and-infrastructure/buildmaster-servers), [environments](/docs/buildmaster/administration-agents-and-infrastructure/environments), and [server roles](/docs/buildmaster/administration-agents-and-infrastructure/server-roles)) as a JSON file. Once exported, you can edit the file (adding or removing any number of infrastructure items), import, and share it with another instance of Otter or BuildMaster, or maintain it as a simple back-up.

![Export Config](/resources/docs/export-config.png){height="" width="50%"}

## Importing Infrastructure Configuration

The *import* function allows you to quickly configure or make massive changes to BuildMaster's infrastructure configuration by importing the configuration file that was exported from another instance of Otter or BuildMaster.

By using the "dry run" option you can see the changes that *would be made* before actively making the changes.

![Import Config](/resources/docs/import-config.png){height="" width="50%"}

## Infrastructure Synchronization

It's quite common to use [Otter](/docs/otter/overview) to manage server configuration, and use BuildMaster to automate application release. Since both use the same set of servers, you can synchronize the infrastructure between multiple instances.

When you enable infrastructure synchronization, your BuildMaster instance connects to either a BuildMaster or Otter instance, pulls configuration from that instance, and updates the infrastructure configuration as needed.

:::(Warning)
When synchronization is enabled, the other instance should be treated as the *source of truth*. There will also be limited options in the Otter UI for editing configuration, as they would almost immediately be wiped out when a synchronization occurs.
:::

To have another instance of BuildMaster or Otter synchronize with your instance, create an API key with view infrastructure permissions.

![Sync](/resources/docs/sync.png){height="" width="50%"}