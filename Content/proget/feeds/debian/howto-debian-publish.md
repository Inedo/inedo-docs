---
title: "HOWTO: Create and Upload Debian Packages to a Private Repository in ProGet"
order: 2
---

ProGet lets you create private Debian repositories (["Feeds"](/docs/proget/feeds/feed-overview) in ProGet) for your internal Debian packages that will allow you to publish, store, and share them within your organization.

This guide will show you how to create and configure a Debian feed in ProGet as a private, custom repository. We'll also talk about how to build, upload, and install packages from this repository.

## Step 1: Create a Debian Feed

To host your internal Debian packages we'll need to create a feed first. You can read more on creating feeds by reading [Creating and Managing Feeds](/docs/proget/feeds/feed-overview#creating-and-managing-feeds)

In your ProGet instance, select "Feeds" and "Create New Feed", and select "Debian Packages" under "System & Software Configuration".

![](){height="" width="50%"}

From here, name the feed. In this case, we'll call it `internal-debian`. After entering the name, click "Create Feed".

![](){height="" width="50%"}

ProGet will create The `internal-debian` and redirect you to the new, empty feed.

![](){height="" width="50%"}

## Step 2: Create an API Key { #api-key }

Next, we'll create an [API Key](/docs/proget/reference-api/proget-apikeys) allowing our local client to authenticate to our `internal-debian` feed when uploading or installing packages. 

To create the API key you will need to fill in the fields by selecting "Feeds (Use Certain Feeds)" as the "Feed Type" and selecting the `internal-debian` feed. Then set the API key. You can use either use an alphanumeric sequence, or just leave it blank to autogenerate one. Make sure the "View/Download" and "Add/Repackage" boxes are checked, and then select "Save".

![](){height="" width="50%"}

## Step 3: Creating a Debian Package

Next, we will create a Debian package. More information on developing these can be found in the [Creating a Debian package](https://debian-handbook.info/browse/stable/debian-packaging.html) chapter of the Debian Administrator's handbook.

### Prerequites

Creating Debian packages requires installing the following tools:   

Running this installs essential tools for creating Debian packages: 

* `build-essential`: Provides a basic development environment, including compilers and libraries necessary for building packages from source. 
* `devscripts`: Contains scripts that assist in creating, building, and managing Debian packages. 
* `dh-make`: Helps generate the initial Debian packaging files required for creating a `.deb` package. 

To install these, run:

```bash
$ sudo apt install build-essential devscripts dh-make 
```

### Create the Package

Navigate to the directory of your package files and create an install file by entering: 

```bash
$ echo "my-package.sh usr/bin" > debian/install  
```

And finally build the package by entering: 

```bash
$ dpkg-buildpackage -u – s uc
```

## Step 4: Upload Your Package to ProGet

To upload your package to your `internal-debian` feed using the [pgutil](/docs/proget/reference-api/proget-pgutil) CLI tool. This will require some [minor configuration](/docs/proget/reference-api/proget-pgutil#sources) such as configuring your ProGet instance and API key as a source by running:

```bash
$ pgutil sources add --name=Default --url=«proget-server» --api-key=«api-key»
```

You can then upload your Debian package by entering:

```bash
$ pgutil packages upload --feed=internal-debian --input-file=«path-to-package»
```

Your package will then be uploaded to the `internal-debian` feed.

![](){height="" width="50%"}

## Step 5: Adding the Feed to Your Local Debian client { #add-feed }

To install packages from your Debian client you will need to add your `internal-debian` feed as a source. This will require the URL found on the feed's page:

![](){height="" width="50%"}

To correctly configure your local client, you will need to perform the following steps:

### 1. Add the Signing Key

To add the signing key to `apt` on  Ubuntu 22.04, run the following command.

```sh
curl -fsSL «proget-server»/internal-debian/keys/internal-debian.asc | sudo gpg --dearmor  -o /etc/apt/keyrings/internal-debian.gpg
```

If you are using an earlier or different distribution, you can use the `apt-key` command instead:

```sh
wget -qO - http://«proget-server»/debian/internal-debian/keys/internal-debian.asc | sudo apt-key add -
```

::: (INFO) 
If this signing key is not created in ProGet and added to your public key ring bundle, you may see a message similar to:  `N: Updating from such a repository can't be done securely, and is therefore disabled by default.`
:::

### 2. Add the Repository Information

Then you'll need to add the repository to the system, which can be done with the following command:

```sh
echo "deb http://api:«api-key»@«proget-server»/debian/internal-debian «distro-name» «component-name»" | sudo tee /etc/apt/sources.list.d/internal-debian.list
```

### 3. (Optional) Remove OSS Repository from Sources

In the case that the OSS repository you are proxying from is already configured in your Debian client, you can remove it with the `sed` command to delete the specific line referencing the repository:

```bash
$ sudo sed -i '/«repository-url»/d' /etc/apt/sources.list
```

If the repository is defined in a file under `/etc/apt/sources.list.d/`, delete the corresponding file:

```bash
$ sudo rm /etc/apt/sources.list.d/«repository-name».list
```

Removing the repository will make sure any packages installed will be sourced from your `internal-debian` feed.

### 4. Update `apt`

In order to actually install packages from a ProGet feed, the package cache must be updated by running:

```sh
sudo apt update
```

## Step 6: (Optional) Confirming Connection to your Debian Feed

You can confirm that your local Debian client is configured with your `internal-debian` feed using the cat command:

```bash
$ cat /etc/apt/sources.list
```

This will list the repositories currently configured, which should include your Debian feed.