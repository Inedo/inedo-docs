---
title: "Debian (Apt)"
order: 11
---

[Debian](https://www.debian.org/) packages are used by any Linux distribution that uses Apt, such as Debian and Ubuntu.

:::(INFO)
Debian feeds have had a significant update in ProGet 2025.14, especially with regard to signing and connector performance, and some of the information here may not fully match the behavior of previous versions. If possible, we recommend upgrading to 2025.14 or later. If you are unable to upgrade at this time, refer to the [archived documentation](https://github.com/Inedo/inedo-docs/blob/9239bd43f4b31c41ef99cb4e9a019c1eb5af6e23/Content/proget/feeds/debian.md).
:::


## PGP Keys and Repository Signing

Debian repositories typically use PGP public/private keys to verify server identity. By default, ProGet will generate a key pair for a Debian feed when the feed is created, but you may generate a new one, delete the key entirely, or upload your own secret key to use instead.

If you are using PGP signing for your feed, you can download the public key from: `https://{proget-server}/debian/{feed-name}/keys/{feed-name}.asc`

To view information about your feed's signing key or to make changes to it, navigate to the *Feed Properties* page and click the *configure* link under Debian Settings >> Signing Key. From here you can download the public key, generate a new secret key, delete your secret key, update its expiration date, or upload your own secret key. Note that to use your own secret key in ProGet it must be RSA 2048-4096 bit. Passphrase protection is optional, but if your key file uses it, you must also supply ProGet with the passphrase. Passphrase or not, ProGet will securely store your key encrypted in its database.


### Adding a Public Key to Apt

Regardless of whether you generate the secret key with ProGet or upload your own, you will need to add its matching public key to apt.

To add a feed's public key for Apt to use, first you need to download and dearmor the public key file:

```sh
sudo curl -fsSL -o /etc/apt/keyrings/{feed-name}.gpg https://{proget-server}/debian/keys/{feed-name}.gpg
```

::: (INFO) 
For convenience, ProGet allows the key to be downloaded in either binary format as above or ASCII-armored. To download the ASCII-armored format instead, replace the `.gpg` extension in the URL with `.asc`.
:::


Next, you need to tell Apt that the key is trusted:

```sh
echo "deb [signed-by=/etc/apt/keyrings/{feed-name}.gpg] https://{proget-server}/debian/{feed-name}/ {distribution} [components]" | sudo tee "/etc/apt/sources.list.d/{feed-name}.list"
```

Test the key configuration by updating your local index:

```sh
sudo apt update
```

::: (INFO) 
If there was an error adding the key or you don't have a key configured in ProGet, you may see a message similar to: `N: Updating from such a repository can't be done securely, and is therefore disabled by default.`
:::


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

## Debian Feed Index File Generation

Debian feeds act like standard Debian repositories and tools such as Apt can request a `Release` file and all associated indexes. ProGet has two modes for creating these index/Release files:

 * **On-demand**: This is the only mode used for versions of ProGet before 2025.14, and remains the default for compatibility reasons. However, it is only appropriate for feeds with few packages. With this mode, your indexes will almost never be stale, but clients will often be kept waiting for ProGet to regenerate its data.
 * **Background**: ProGet will monitor the feed for changes and proactively generate updated indexes in the background as needed. Indexes could be slightly out of date based on the user-defined maximum age setting, but users will never be kept waiting when requesting an updated index.

**We recommend using the background mode for almost all feeds**, and this will likely become the default in ProGet 2026. The only reason on-demand remains the default is that we did not want to introduce an automatic behavior change as part of a maintenance release.

### Enabling Background Index File Generation

To enable background index generation for a feed, navigate to the *Feed Properties* page and click the *configure* link for the *Index File Generation* setting. Check the box and optionally change the default max age value.

::: (INFO) 
Connectors **also** have their own background index generation setting that is independent of the feed setting. We also strongly recommend enabling this - especially for connectors to large repositories. See the section below for more information.
:::


## Connectors for Debian Feeds

Debian feeds support connectors to other ProGet Debian (Apt) feeds, official repositories, and other third-party repositories.

### Connector Index File Generation

ProGet has to download the index file for any connector repositories. For official repositories (or any that are very large), we strongly recommending enabling background index updating for the connector. You can enable this by clicking the *edit* link on the *Basic Properties* section of the connector page and specifying a value (in minutes) for the *Index poll frequency* field. When a value is specified for that field, ProGet will check the remote index for changes with this frequency and keep its local indexes up to date with minimal impact to users. If this field is left blank, ProGet will continue to use its old behavior of only updating this index on demand, which could result in very slow response times for large, frequently updated repositories.

### Official Repositories

When creating a connector to an official repository, we recommend a name based on the distribution and components (if specified). For example:

<table>
    <tr><td>URL:</td><td><code>https://ftp.debian.org/debian/</code></td></tr>
    <tr><td>Distribution:</td><td><code>buster</code></td></tr>
    <tr><td>Components:</td><td><em>all</em></td></tr>
    <tr><td>Connector name:</td><td><code>debian-buster</code></td></tr>
</table>


### Limitations

 - ProGet cannot create a Debian repository snapshot
