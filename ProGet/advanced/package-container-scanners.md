---
title: Package/Container Scanners
sequence: 700
keywords: proget
subtitle: Package/Container Scanners
show-headings-in-nav: true
---

ProGet can connect to an external resource such as Otter or Kubernetes to collect and display how packages and Docker container images are used across your infrastructure.

## Package/Container Scanners

To begin displaying package/container usage, you must first configure a package/container scanner. Go to the *Administration->Package/Container Scanners* page, and click the *Create New Scanner* button.

### Configuring Otter

Otter can be configured to collect both package and container usage from your servers. See [server packages] in the Otter documentation for instructions on configuring this feature.

Once Otter has been configured to collect package and/or container data, you will need to create an API key that allows access to the *Package/Container Usage API*. Once this key has been created, you are ready to add the Otter scanner to ProGet.

### Configuring Kubernetes

## Viewing Container Usage

Once data has been collected, container images in ProGet will now have a *Usage* tab. Click this tab to display a list of every server that is using the image, along with other relevant information such as container name and status.