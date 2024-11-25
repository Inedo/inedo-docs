---
title: "HOWTO: Create and Push Chocolatey Packages to a Private Repository in ProGet"
order: 2
---

ProGet can be configured as a private [Chocolatey](https://chocolatey.org) repository and host all your Chocolatey packages, allowing you to push, store and share then within your organization.

Hosting your Chocolatey packages in ProGet allows you to bypass [Chocolatey's rate-limiting policies](https://docs.chocolatey.org/en-us/community-repository/community-packages-disclaimer), especially if you enable [metadata caching](/docs/proget/feeds/connector-overview#connector-caching) and internalize your packages.

This guide will walk you through the process of setting up a Chocolatey ["Feed"](/docs/proget/feeds/feed-overview) in ProGet as a private, custom repository. We'll cover how to create, push, and install Chocolatey packages from this repository, as well as how to internalize packages to let your Chocolatey packages be used in air gapped or offline environments.

## Step 1: Create and Name a Chocolatey Feed { #create-feed }

Start by creating a Chocolatey feed to host your internal packages. Navigate to "Feeds" and "Create New Feed". Then select "Chocolatey Packages".

![](/resources/docs/proget-chocolatey-newfeed.png){height="" width="50%"}

Now select "No Connectors (Private packages only)" as this feed will be intended to host private packages. From here, name the feed. For this example, we will call it `internal-Chocolatey`, and then click "Create Feed".

![](/resources/docs/proget-chocolatey-newfeed-internal.png){height="" width="50%"}

You'll then see several options related to ProGet's [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) features. These are only for users looking to use third-party packages. Leave these boxes unchecked, and select "Set Feed Features". You will then be directed to the new `internal-chocolatey` feed, currently empty.

![](/resources/docs/proget-chocolatey-internal-empty.png){height="" width="50%"}

## Step 2: Create a Chocolatey Package

To create a Chocolatey package, you will need to start by opening an elevated PowerShell and running the `choco new` command:

```bash
$ choco new «package-name»
```

From here you will need to create and edit the necessary package files such as the `.nuspec` and `chocolateyInstall.ps1`, and then build the package with the choco pack command:

```bash
$ choco pack
```

You can read more about creating a Chocolatey package in [Chocolatey's Official Documentation](https://docs.chocolatey.org/en-us/features/create-packages/).

## Step 3: Adding ProGet As A Source  { #add-source }

To add your `internal-chocolatey` feed as a source in your client, use the `choco source add` command. We recommend giving the source the same name as your feed. For example, if adding the ProGet server `proget.corp.local` as a source, you would enter:

```bash
$ choco source add -n="internal-chocolatey" -s="http://proget.corp.local/feeds/internal-chocolatey" --priority=1 
```

To make sure that your client only installs Chocolatey Packages from ProGet, we recommend removing any default sources already configured using the `choco source remove` command:

```bash
$ choco source remove -n="chocolatey"
```
You can confirm your client is properly connected to ProGet using the `choco source list` command:

```bash
$ choco source list
```

## Step 4: Create an API Key

We will now create an [API Key](/docs/proget/reference-api/proget-apikeys) allowing our local client to authenticate to our `internal-chocolatey` feed. This allows us to push packages to the feed.

When creating an API Key, fill in the fields by selecting "Feeds (Use Certain Feeds)" as the "Feed Type" and selecting the `internal-chocolatey` feed. Then set the API key. You can use any alphanumeric sequence, or just leave it blank to autogenerate one. Ensure that the "View/Download" and "Add/Repackage" boxes are checked, and then select "Save".

![](/resources/docs/proget-chocolatey-apikey-internal.png){height="" width="50%"}

## Step 5: Pushing Your Package to Your Chocolatey Feed

To push a package use the `choco push` command:  

```bash
$ choco push «path-to-package» --source=internal-chocolatey --api-key=«api-key»
```

For example, if pushing the Chocolatey package MyApplication-1.2.3.nupkg stored at `C:\chocolatey_packages\` to your `internal-chocolatey` feed with the API key `abc12345` you would enter:

```bash
$ choco push C:\chocolatey_packages\MyApplication-1.2.3.nupkg --source=internal-chocolatey --api-key=abc12345
```

This will upload it to your `internal-chocolatey` feed:

![](/resources/docs/proget-chocolatey-internal-uploaded.png){height="" width="50%"}

## (Optional) Internalizing Chocolatey Packages

If you want to minimizing reliance on external sources for software installed using Chocolatey Packages, you can [internalize your packages](https://blog.inedo.com/chocolatey/internalization). 

Chocolatey’s [package internalizer](https://docs.chocolatey.org/en-us/features/package-internalizer) can internalize packages automatically or you can do it yourself. To learn more on how to do this you can read [HOWTO: Set Up a Private Chocolatey Repository for Internalized Packages](/docs/proget/feeds/chocolatey/howto-chocolatey-internalized)

