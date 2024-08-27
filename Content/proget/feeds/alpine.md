---
title: "Alpine (APK)"
order: 16
---

[Alpine Linux](https://www.alpinelinux.org/) is a lightweight security-focused Linux distribution that is popular for use in Docker containers and embedded systems because of its small footprint. Its package manager is Alpine Package Keeper (APK).

Starting in ProGet 2023.22, you can create Alpine (APK) feeds to host packages for Alpine Linux.

## Prerequisite Configuration

## Client Configuration

In order to install APK packages from ProGet, each client must perform the following steps:

### 1. Trust the feed's public key

APK stores trusted public keys for package repositories in `/etc/apk/keys`. To trust a ProGet APK feed, save a copy of the feed's public key in that directory using the following naming scheme:

```bash
wget -O "/etc/apk/keys/<FeedName>@proget.rsa.pub" <ProGetUrl>/apk/<FeedName>/keys/<FeedName>%40proget.rsa.pub
```

Note that you can configure a different name for your public key if necessary. By default, it will be `<FeedName>@proget.rsa.pub` for all Alpine feeds.

### 2. Add the feed to APK's repositories file

The list of repositories to use is stored in the `/etc/apk/repositories` file. It is a plain text file with a repository URL on each line. To add a ProGet feed to the end of the list:

```bash
echo "<ProGetUrl>/apk/<FeedName>/" >> /etc/apk/repositories
```

### 3. Verify APK configuration

To verify that APK is able to download the package index, execute:

```bash
apk update
```

If the configuration is correct, this will list all repos (including the ProGet feed) without errors.

## Common Tasks

## Installing Packages

Alpine packages are installed using APK. To install a package hosted by ProGet, use the command:

```bash
apk add <package-name>
```

## Creating Packages

To learn how to create an APK package, visit a resource such as [Creating an Alpine package](https://wiki.alpinelinux.org/wiki/Creating_an_Alpine_package) on the Alpine Linux wiki.

## Publishing Packages

`APK` does not support uploading a package, so ProGet offers a few alternative methods to publish APK packages:

### Upload from ProGet Web Application

On the feed overview page, select "Add Package" and "Upload Package" to upload an `.rpm` file directly from your browser.

#### Publish via HTTP

To push an APK package via HTTP, issue a `PUT` or `POST` request with the package file as the content to: `http://{proget-server}/apk/{feed-name}/`

#### Examples:

#### cURL:

```bash
curl http://{proget-server}/apk/{feed-name}/ --user <user>:<password> --upload-file {my-package}.apk
```

#### wget:

```bash
wget http://{proget-server}/apk/{feed-name}/ --http-user <user> --http-password <password> --method POST --body-file {my-package}.apk
```

#### PowerShell:

```powershell
Invoke-WebRequest http://{proget-server}/apk/{feed-name}/ -Credential [System.Net.NetworkCredential]::new('<user>', '<password>') -Method PUT -InFile {my-package}.apk
```

## Connectors for Alpine (APK)

Alpine (APK) feeds support connectors to other ProGet Alpine (APK) feeds, official Alpine repositories, and other third-party repositories.

### Official Alpine Repositories

AlpineLinux.org hosts dozens of different Alpine (APK) repositories; there's generally one repository per distro (3.17, 3.18, etc.), quality (main, community, releases), and architecture (x86_64, armv7, etc.). 

When creating a connector and connecting to an official repository, we recommend using a name that follows the URL conventions. For example:

```html
<table>
    <tr><td>URL:</td><td><code>https://dl-cdn.alpinelinux.org/alpine/v3.18/main/x86_64/</code></td></tr>
    <tr><td>Connector name:</td><td><code>alpine-v3.18-main-x86_64</code></td></tr>
</table>
```

You can see a list of the default repositories in the `/etc/apk/repositories` file on a new Alpine installation.