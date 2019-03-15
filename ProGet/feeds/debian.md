---
title: Debian (apt)
subtitle: Debian Feeds in ProGet
sequence: 500
keywords: proget,feeds,debian,apt
show-headings-in-nav: true
---

[Debian](https://www.debian.org/) is a free operating system whose programs are distributed using packages that can be installed with APT. As of ProGet v5.2, Debian packages can be hosted and installed from ProGet.

## Pre-requisite Configuration

### Configure the Signing Key in ProGet

In order to serve packages from a Debian feed, a signing key must be created within ProGet. Once a feed is created, visit the Manage Feed page, and select "manage keys" in the Properties panel. Enter a description (just use something like "proget" or "default" if creating a single one), and click "Generate Key".

After a brief delay, the key will be generated, and its fingerprint visible in the Manage Keys window.

### Client Configuration

In order to actually install packages from ProGet, each client must perform the following steps:

##### 1. Add the signing key

Without adding the signing key, you will see a message similar to: 

`N: Updating from such a repository can't be done securely, and is therefore disabled by default.`

To add the key to `apt`, run the following commands:

```
wget -O "deb.gpg" http://{proget-server}/debian-feeds/{feed-name}.pub && sudo apt-key add "{feed-name}.gpg" 
echo "deb http://{proget-server}/ {feed-name} {component-name}" | sudo tee /etc/apt/sources.list.d/{proget-deb}.list
```

##### 2. Update `apt`

In order to actually install packages from a ProGet feed, the package cache must be updated by running:

```
sudo apt update
```

## Common Tasks

### Installing Packages

Debian packages are installed using [`apt-get`](https://manpages.debian.org/stretch/apt/apt-get.html). To install a package hosted by ProGet, use the command: 

```

```

### Creating Packages



### Publishing Packages


```

```

#### Technical Limitations

 - Connectors are not support for Debian feeds