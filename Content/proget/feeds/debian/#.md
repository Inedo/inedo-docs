---
title: "Debian (Apt)"
order: 11
---

[Debian](https://www.debian.org/) is a free operating system whose programs are distributed via packages that can be installed with APT.

:::(Warning)
This documentation refers to the "new" type of Debian feeds added in ProGet 2023.22. If you're using an older version of ProGet, see [Debian (Legacy) Feed Types](#debian-legacy-feed-types) to learn how to configure the "older" type of Debian feed.
:::

## Prerequisite Configuration

In order to install packages from ProGet, each client must perform the following steps:

### 1. Add the signing key

To add the signing key to `apt` on  Ubuntu 22.04, run the following command.

```sh
curl -fsSL {proget-server}/${feed-name}/keys/${feed-name}.asc | sudo gpg --dearmor  -o /etc/apt/keyrings/${feed-name}.gpg
```

If you are using an earlier or different distribution, you can use the `apt-key` command instead:

```sh
wget -qO - http://{proget-server}/debian/{feed-name}/keys/{feed-name}.asc | sudo apt-key add -
```

::: (INFO) 
If this signing key is not created in ProGet and added to your public key ring bundle, you may see a message similar to:  `N: Updating from such a repository can't be done securely, and is therefore disabled by default.`
:::

### 2. Add the  repository information

Then you'll need to add the repository to the system, which can be done with the following command:

```sh
echo "deb http://{proget-server}/debian/{feed-name} {distro-name} {component-name}" | sudo tee /etc/apt/sources.list.d/{feed-name}.list
```

### 3. Update `apt`

In order to actually install packages from a ProGet feed, the package cache must be updated by running:

```sh
sudo apt update
```

## Common Tasks

### Installing Packages 

Debian packages are installed using [`apt-get`](https://manpages.debian.org/stretch/apt/apt-get.html). To install a package hosted by ProGet, use the command: 

```sh
sudo apt install "{package-name}"
```

### Creating Packages  

To learn how to create a Debian package, visit the [Creating a Debian package](https://debian-handbook.info/browse/stable/debian-packaging.html) chapter of the Debian Administrator's handbook.

### Publishing Packages  

`apt` does not support uploading a package, so ProGet offers a few alternative methods to publish Debian packages:

#### Upload from ProGet Web Application

On the feed overview page, select "Add Package" and "Upload Debian Package" to upload a `.deb` file generated from the `dpkg-deb` command.

#### Publish via HTTP

To push a Debian package via HTTP, issue a PUT or POST request with the package file as the content to: `http://{proget-server}/debian/{feed-name}/upload/{distro-name}/{component-name}`

#### Examples:

#### cURL:

```sh
curl http://{proget-server}/debian/{feed-name}/upload/{distro-name}/{component-name} --user <user>:<password> --upload-file {my-package}.deb
```

#### wget:

```sh
wget http://{proget-server}/debian/{feed-name}/upload/{distro-name}/{component-name} --http-user <user> --http-password <password> --method POST --body-file {my-package}.deb
```

#### PowerShell:

```powershell
Invoke-WebRequest http://{proget-server}/debian/{feed-name}/upload/{distro-name}/{component-name} -Credential [System.Net.NetworkCredential]::new('<user>', '<password>') -Method PUT -InFile {my-package}.deb
```

### Source Packages

ProGet does not currently support [Debian source packages](https://wiki.debian.org/Packaging/SourcePackage), and attempting to upload a source package (i.e. if the extension does not equal `*.deb`) will fail. 

A user has asked us to add support for source packages for the following reasons:

*Source packages can often be useful when using the Debian packaging tools (apt, apt-get, aptitude) to download the source package, especially for debugging purposes. Without the source package, one can only get the stack trace, but not really inspect the code. Also, when upgrading from one Debian release (Ubuntu 19.10) to the next version (Ubuntu 20.04), the recommended method is to download all source packages from the previous repository and then rebuild them in the new operating system. This is the standard best practice for seeding a new apt repository

If Source Packages are a feature that would be of interest to you, please [contribute in the forums](https://forums.inedo.com/); users with paid versions of our software can also [submit a ticket](https://inedo.com/support/ticket).

## Debian (Legacy) Feed Types

In ProGet 2023.22, we [created a new Debian feed type](https://blog.inedo.com/inedo/new-debian-feeds) called "Debian (New)" feeds, and called the old feeds to "Debian (Classic)". In ProGet 2024, the old feeds are called "Debian (Legacy)" and the new are simply "Debian".

The key differences between the feed types are that Debian (Legacy) feeds tie distribution to feeds and do not support connectors. 

### Migration from Debian (Legacy) Feeds

You can migrate packages in this (and other) Debian feeds to the new  feed type. Debian (Legacy) feeds will be considered a legacy feature in ProGet 2024 and likely removed in a future version.

### Signing Keys in Debian (Legacy) Feeds

In order to serve packages from a Debian (Legacy) feed, a signing key must be created in ProGet. Once a feed is created, visit the Manage Feed page and select "manage keys" in the Properties panel. Enter a description (just use something like "proget" or "default" if you are creating a single feed) and click "Generate Key". After a short delay, the key is generated and its fingerprint is displayed in the Manage Keys window.

### Distributions & Feed Names 

In Debian (Legacy) feeds, the feed name served as the distribution name. Therefore, to add the repository to your  system, you would need to run the following command:

```sh
echo "deb http://{proget-server}/ {feed-name} {component-name}" | sudo tee /etc/apt/sources.list.d/{feed-name}.list
```

## Connectors for Debian (Apt) Feeds

Starting in ProGet 2023.22, Debian (Apt) feeds support connectors to other ProGet Debian (Apt) feeds, official RPM repositories, and other third-party repositories.

### Official  Repositories

When connecting to an official Debian (Apt) repository, ProGet will periodically download the index file. This can take 30-60+ seconds, and the page will only report "Loading Packages..."; you may get a database lock error if you refresh, but it should eventually work. We will improve this experience in a future version of ProGet!

When creating a connector to an official repository, we recommend a name based on the distribution and components (if specified). For example:

```html
<table>
    <tr><td>URL:</td><td><code>http://ftp.debian.org/debian/</code></td></tr>
    <tr><td>Distribution:</td><td><code>buster</code></td></tr>
    <tr><td>Components:</td><td><em>all</em></td></tr>
    <tr><td>Connector name:</td><td><code>debian-buster</code></td></tr>
</table>
```

### Snapshot Repositories

Although "Debian repository snapshots" are a feature in other products like Aptly, ProGet does not support them. 

Snapshots seem relatively easy to support (basically, we would just disable periodic updates on "snapshot" connectors), but we don't really understand the use case and how users would want to use them.

We surveyed several Debian feed users, and felt this comment captured the general sentiment.

> "repository snapshots are archaic; they made sense a long time ago, but Docker changed all that. It's so much simpler to use container images like FROM debian:buster-20230919. That's effectively our snapshot, and when we need to main old releases (which happens more often than I'd like), we just rebuild the image from that. The other big advantage is that build time is easily 10x faster if not more."

If snapshot repositories are a feature that would be of interest to you, please [contribute in the forums](https://forums.inedo.com/); users with paid versions of our software can also [submit a ticket](https://inedo.com/support/ticket).
