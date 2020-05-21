---
title: Debian (apt)
subtitle: Debian Feeds in ProGet
sequence: 500
keywords: proget,feeds,debian,apt
show-headings-in-nav: true
---

[Debian](https://www.debian.org/) is a free operating system whose programs are distributed using packages that can be installed with APT. As of ProGet v5.2, Debian packages can be hosted and installed from ProGet.

## Pre-requisite Configuration {#prerequisite data-title="Pre-requisite Configuration"}

### Configure the Signing Key in ProGet

In order to serve packages from a Debian feed, a signing key must be created within ProGet. Once a feed is created, visit the Manage Feed page, and select "manage keys" in the Properties panel. Enter a description (just use something like "proget" or "default" if creating a single one), and click "Generate Key".

After a brief delay, the key will be generated, and its fingerprint visible in the Manage Keys window.

### Client Configuration

In order to actually install packages from ProGet, each client must perform the following steps:

##### 1. Add the signing key

To add the key to `apt`, run the following commands:

```
wget -O "deb.gpg" http://{proget-server}/debian-feeds/{feed-name}.pub && sudo apt-key add "{feed-name}.gpg" 
echo "deb http://{proget-server}/ {feed-name} {component-name}" | sudo tee /etc/apt/sources.list.d/{proget-deb}.list
```

If this signing key is not created in ProGet and added to your public key ring bundle, you may see a message similar to: 

`N: Updating from such a repository can't be done securely, and is therefore disabled by default.`

##### 2. Update `apt`

In order to actually install packages from a ProGet feed, the package cache must be updated by running:

```
sudo apt update
```

## Common Tasks

### Installing Packages {#installing data-title="Installing Packages"}

Debian packages are installed using [`apt-get`](https://manpages.debian.org/stretch/apt/apt-get.html). To install a package hosted by ProGet, use the command: 

```
sudo apt install "{package-name}"
```

### Creating Packages  {#creating data-title="Creating Packages"}

To learn how to create a Debian package, visit the [Creating a Debian package](https://debian-handbook.info/browse/stable/debian-packaging.html) chapter of the Debian Administrator's handbook.

### Publishing Packages  {#publihsing data-title="Publishing Packages"}

`apt` does not support uploading a package, so ProGet offers a few alternative methods to publish Debian packages:

#### Upload from ProGet Web Application

On the feed overview page, select "Add Package" and "Upload Debian Package" to upload a `.deb` file generated from the `dpkg-deb` command.

#### Publish via HTTP

To push a Debian package via HTTP, issue a PUT or POST request with the package file as the content to: `http://{proget-server}/debian-packages/upload/{feed-name}/{component-name}/{file-name}.deb`

##### Examples:

###### cURL:

```
curl http://{proget-server}/debian-packages/upload/{feed-name}/{component-name}/{package-name}.deb --user <user>:<password> --upload-file {my-package}.deb
```

###### wget:

```
wget http://{proget-server}/debian-packages/upload/{feed-name}/{component-name}/{package-name}.deb --http-user <user> --http-password <password> --method POST --body-file {my-package}.deb
```

###### PowerShell:

```
Invoke-WebRequest http://{proget-server}/debian-packages/upload/{feed-name}/{component-name}/{package-name}.deb -Credential [System.Net.NetworkCredential]::new('<user>', '<password>') -Method PUT -InFile {my-package}.deb
```


## Technical Limitations  {#limitations data-title="Technical Limitations"}

Please 

 - Connectors are not support for Debian feeds
 - `InRelease` endpoint (i.e. the clear-signed index) is not supported due to issues with the BouncyCastle library generating invalid armored output; if there is a demand for this, we can consider it in a future maintenance release

### Source Packages {#sourcepackages data-title="Source Packages"}

ProGet does not currently support [Debian source packages](https://wiki.debian.org/Packaging/SourcePackage), and attempting to upload a source package (i.e. if the extension does not equal `*.deb`) will fail. 

We've had one user request that we add support for source packages, for the followign reasons:

*Source packages can often be useful using the Debian packaging tools (apt, apt-get, aptitude) to download the source package, especially for debugging purposes. Without the source package, one can only get the stack trace but can't actually inspect the code. Additionally, the recommended method from upgrading from one Debian release (Ubuntu 19.10) to the next version (Ubuntu 20.04) is to download all the source packages from the previous repository, and then rebuild them in the new operating system. That is standard best practices for seeding a new apt repository.*

If this is a feature that woudl be of interest to you, please [contribute in the forums](https://forums.inedo.com/); users with paid versions of our software can also [submit a ticket](https://inedo.com/support/ticket).
