---
title: Offline Installs
sequence: 20
keywords: proget, installation
show-headings-in-nav: true
---

## Offline Installs {#offline data-title="Offline Installs"}

Inedo Hub can be configured to operate in an offline configuration. This is useful if you need to update an Inedo product on a server
that does not have Internet access. To create an offline Inedo Hub, follow these steps:

1. Install Inedo Hub on a workstation/server that has access to the Internet.
2. Click the "Create Offline Installer" button on the main page of the Inedo Hub application.
3. Select the versions of Inedo products you'd like to include in the offline installer. By default, the latest stable version of each product is selected.
4. Select the destination directory where the offline installer will be created. This directory will be created if it does not exist. If it does exist, it must be empty.
5. Click the "Create Installer" button and wait for the packages to be downloaded.
6. Copy the files in the destination directory to the server where you'd like to install one of the selected products.
7. Run InedoHub.exe on the server, and install the desired app.
