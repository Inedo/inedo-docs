---
title: Offline Installs
sequence: 20
keywords: proget, installation
show-headings-in-nav: true
---

## Offline Installs {#offline data-title="Offline Installs"}

Installable product packages are Universal Packages, and the hub uses a universal package feed hosted at inedo.com to download product releases and updates for the hub itself.

It is still possible to use the Inedo Hub to install/manage products on a server that is not connected to the Internet, but it will take a little extra configuration. In order for the software to work, it needs to be able to query a Universal Package Feed for available product releases - you have two options for getting this to work on a disconnected server:
{.docs}
1. **Use Proget** - If you have a ProGet instance on your local network, you can create a Universal Package Feed in it and then create a connector to the Inedo [product releases feed](https://inedo.com/products/feed). Make a note of the feed API Endpoint URL displayed in ProGet for the newly-created feed.
2. **Use Directory** - Download the packages you need to a local directory. At a minimum, this should be the latest DesktopHub package and the desired ProGet package.

Once you have completed one of the above options, click the [config] link in the Inedo Hub and specify either the ProGet Feed URL or the file system directory containing downloaded packages for the Package Source field.
