---
title: Configuration Export, Import, & Sync
subtitle: Export, Import, & Sync
keywords: ""
show-headings-in-nav: true
---

Otter maintains your servers' configuration using an *infrastructure as code* approach, but also gives you the ability to import and export Otter's very own *configuration as code*. This makes sharing and maintaining your infrastructure configuration that much easier.

## Exporting Infrastructure Configuration {#export data-title="Exporting Infrastructure"}

You can export all of your instance's infrastructure configuration ([servers], [environments], and [server roles]) as a JSON file. Once exported, you can simply edit the file (adding or removing any number of infrastructure items) and import, share it with another instance of Otter or BuildMaster, or maintain it as a simple back-up.

![export configuration](/resources/documentation/otter/export-config.png){.screenshot}

## Importing Infrastructure Configuration {#import data-title="Importing Infrastructure"}

The *import* function allows you to quickly configure or make massive changes to Otter's infrastructure configuration by importing the configuration file that was exported from another instance of Otter or BuildMaster.

By using the "dry run" option you can see the changes that *would be made* before actively making the changes.

![import configuration](/resources/documentation/otter/import-config.png){.screenshot}

## Infrastructure Synchronization {#sync data-title="Infrastructure Synchronization"}

It's quite common to use Otter for managing server configuration, and [BuildMaster] for application release automation; because they both use the same set of servers, you can synchronize the infrastructure between multiple instances.

When you enable infrastructure synchronization, your Otter instance connects to either a BuildMaster or Otter instance, pulls configuration from that instance, and updates the infrastructure configuration as needed.

When synchronization is enabled, the other instance should be treated as the *source of truth*. There will also be limited options in the UI for editing configuration, as they would almost immediately be wiped-out when a synchronization occurs. {.announcement}

To have another instance of BuildMaster or Otter synchronize with your instance, simply create an API key with view infrastructure permissions.

![Synchronization Settings](/resources/documentation/buildmaster/sync.png){.screenshot}

[servers]: /docs/otter/core-concepts/servers
[environments]: /docs/otter/modeling-infrastructure/environments
[server roles]: /docs/otter/modeling-infrastructure/server-roles
[BuildMaster]: /buildmaster
