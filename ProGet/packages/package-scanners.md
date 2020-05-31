---
title: Live Package Usage Scanning
sequence: 500
keywords: proget
subtitle: Package Usage Scanning
show-headings-in-nav: true
---

ProGet can connect to an external resource such as Otter to collect and display how packages are used across your infrastructure.

To begin displaying package usage, you must first configure a package scanner; this is done on the Manage Feed > Usage & Scanning page.

### Configuring Otter
Otter can be used to collect package usage from your servers. See server packages in the Otter documentation for instructions on configuring this feature.

Once Otter has been configured to collect package data, you will need to create an API key that allows access to the *Package/Container Usage API*. Once this key has been created, you are ready to add the Otter scanner to ProGet.

Once data has been collected, packages in ProGet will now have a *Usage* tab. Click this tab to display a list of every server that is using the image, along with other relevant information such as container name and status.