---
title: "Package Usage Scanning"
order: 6
---

ProGet can connect to our server provisioning and configuration management tool, [Otter](https://inedo.com/otter/), to capture and display how packages are used in your infrastructure. This allows you to see which servers have had packages installed or removed. This way, you can easily ensure that the correct packages are installed, and you can quickly find [vulnerable packages](/docs/proget/sca/vulnerabilities) that have been installed so that you can remove them.

This feature is available in all versions of ProGet, but Free users must add entries manually. The only feed types that currently support live package scans are  [Chocolately](/docs/proget/feeds/chocolatey), [PowerShell](/docs/proget/feeds/powershell), [RPM (yum)](/docs/proget/feeds/rpm), [Debian](/docs/proget/feeds/debian), and [UPack](/docs/proget/feeds/universal). This feature is also extensible, so you can create a custom ProGet extension to connect to another server provisioning tool.

## Configuring ProGet 
To start viewing package usage, simply create a feed that supports this feature. Live package scanning is already enabled, but you can confirm this on the Manage Feed page.

## Configuring Otter 
Otter can be used to collect package usage from your servers. See [server packages in the Otter documentation](/docs/otter/connecting-to-your-servers-with-otter/otter-servers-in-otter#packagess) for instructions on configuring this feature.

Once Otter is configured to collect package data, you must [create an API key](/docs/proget/api/apikeys) that allows access to the *Package/Container Usage API*. 

![create_API_key_pckguse.png](/resources/docs/create_API_key_pckguse.png){height="" width=""}

## Viewing Live Package Usage Data

After data collection, the data for the packages will be displayed in ProGet on the "Usage & Statistics" tab. On the individual package's Overview page, click this tab to see a list of all servers using that package, along with other information such as name, status, time, and so on.

