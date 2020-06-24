---
title: Live Package Usage Scanning
sequence: 500
keywords: proget
subtitle: Package Usage Scanning
show-headings-in-nav: true
---

ProGet can connect to our server provisioning and configuration management tool, [Otter](https://inedo.com/otter/), to collect and display how packages are used across your infrastructure. This allows you to see on which servers packages have been installed or removed. This makes it easy to ensure the right packages are installed and helps you find [vulnerable packages](/docs/proget/compliance/vulnerabilities) that have been installed quickly so you may remove them.

This feature is available in all versions of ProGet, but Free users must manually add entries. The only feed types that support live package scanning at this time are [Chocolately](/docs/proget/feeds/chocolatey), [PowerShell](/docs/proget/feeds/powershell), [RPM (yum)](/docs/proget/feeds/rpm), [Debian](/docs/proget/feeds/debian), and [UPack](/docs/proget/reference/universal-package-feed-reference). This feature is also extensible so you can create a custom ProGet extension to connect to another server provisioning tool.

## Configuring ProGet {#proget-config data-title="Configuring ProGet"}
To begin displaying package usage, simply create a feed that supports this feature. Live package scanning is already enabled, but you can confirm this from the Manage Feed page.

{.screenshot} OF MANAGE FEED PAGE

## Configuring Otter {#otter-config data-title="Configuring Otter"}
Otter can be used to collect package usage from your servers. See [server packages in the Otter documentation](/docs/otter/core-concepts/servers#packages) for instructions on configuring this feature.

Once Otter has been configured to collect package data, you will need to [create an API key](/docs/proget/administration/security/api-keys) that allows access to the *Package/Container Usage API*. 

{.screenshot} OF API KEY ADDITION

## Viewing Live Package Usage Data {#view-live-data data-title="Viewing Live Package Usage Data"}

Once data has been collected, packages in ProGet will now have data in the "Usage & Statistics" tab. From the individual package's Overview page, click this tab to display a list of every server using that package, along with other information like name, status, time, etc.

{.screenshot} OF SERVERS DISPLAYED
