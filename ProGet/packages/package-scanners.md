---
title: Live Package Usage Scanning
sequence: 500
keywords: proget
subtitle: Package Usage Scanning
show-headings-in-nav: true
---

ProGet can connect to Inedo's server provisioning tool, [Otter](https://inedo.com/otter/), to collect and display how packages are used across your infrastructure. This allows you to see not just that packages are running in your infrastructure but also on which servers they have been installed or removed. This makes it easy to make sure your infrastructure is running on the right packages and helps you find and delete [vulnerable packages](/docs/proget/compliance/vulnerabilities) quickly.

This feature is available in all versions of ProGet, but Free users must manually add entries. This feature is also extensible so you can create a custom ProGet extension to connect to another server provisioning tool.

## Configuring ProGet {#proget-config data-title="Configuring ProGet"}
To begin displaying package usage, you must first enable package download statistics; this is done at Manage Feed > Properties > Change "Package Statistics."

{.screenshot} OF ENABLE PACKAGE STATS

Once ProGet is able to record package download statistics, you can configure Otter to "talk to" ProGet to provide live package usage information.

## Configuring Otter {#otter-config data-title="Configuring Otter"}
Otter can be used to collect package usage from your servers. See [server packages in the Otter documentation](/docs/otter/core-concepts/servers#packages) for instructions on configuring this feature.

Once Otter has been configured to collect package data, you will need to [create an API key](/docs/proget/administration/security/api-keys) that allows access to the *Package/Container Usage API*. Once this key has been created, you are ready to add the Otter scanner to ProGet.

{.screenshot} OF API KEY ADDITION

## Viewing Live Package Usage Data {#view-live-data data-title="Viewing Live Package Usage Data"}

Once data has been collected, packages in ProGet will now have data in the *Usage & Statistics* tab. From the individual package's Overview page, click this tab to display a list of every server using that package, along with other information like name and status.

{.screenshot} OF WHAT DISPLAYS
