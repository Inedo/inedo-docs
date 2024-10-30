---
title: "HOWTO: Set Up a Private Chocolatey Repository for Internalized Packages"
order: 3
---

ProGet can be configured as a private Chocolatey repository used to host internalized packages exclusively. 

For more information on how it works, along with an explanation, see [Chocolatey Privatization and Internalization](https://blog.inedo.com/chocolatey/internalization). However, in this article, we will explain how to set up an internalized feed in ProGet to act as a private Chocolatey repository.

## Step 1: Internalizing Your Packages

To internalize Chocolatey Packages, you can use Chocolatey’s [package internalizer](https://docs.chocolatey.org/en-us/features/package-internalizer) to internalize packages automatically or you can do it yourself:

1. Download and unpack the existing package as a .zip file.
2. Download the resources that the package has and add them to the package.
3. Edit the installation script to point to the internal software.
4. Package it back up.

Be sure to change the title or edit the description of your internalized packages before uploading them to ProGet, as internalized packages look externally identical to normal packages. To avoid confusion, we recommend slightly changing the name of the internalized package, for example, change "Firefox" to "Firefox-internalized".

## Step 3: Create an Internalized Feed in ProGet

Now that your packages are internalized, a separate feed should be created exclusively for approved internalized Chocolatey packages. You can read more on creating feeds by reading [Creating and Managing Feeds](/docs/proget/feeds/feed-overview#creating-and-managing-feeds)

In your ProGet instance, select "Feeds" and "Create New Feed", and select "Chocolatey Packages".

![](){height="" width="50%"}

Now select "No Connectors (Private packages only)" as this feed will be intended as a private repository.

![](){height="" width="50%"}

From here, we name our feed. For this example, we will call it `internalized-chocolatey`, and then click "Create Feed".

![](){height="" width="50%"}

You'll then see several options related to ProGet's [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) features. These are only for users looking to use third-party packages like those on [Chocolatey.org](https://chocolatey.org). Leave these boxes unchecked, and select "Set Feed Features". You will then be directed to the new `internalized-chocolatey` feed, currently empty.

![](){height="" width="50%"}

## Step 3: Create an API Key

Now create an [API Key](/docs/proget/reference-api/proget-apikeys) which will allow you to authenticate to the `internalized-chocolatey` feed to upload your internalized packages to it.

You can read more about creating API keys in ProGet on our [API Key](/docs/proget/reference-api/proget-apikeys) page.

When creating an API Key, fill in the fields by selecting "Feeds (Use Certain Feeds)" as the "Feed Type" and selecting the `internalized-chocolatey` feed. Then set the API key. You can use any alphanumeric sequence, or just leave it blank to autogenerate one.

![](){height="" width="50%"}

Make sure the "View/Download" and "Add/Repackage" boxes are checked, and then select "Save".

## Step 4: Upload Packages to Internalized Feed

Now that you have a private internalized feed set up, you can upload your internalized packages to it. To do this you can use [pgutil](/docs/proget/reference-api/proget-pgutil).

pgutil will require some [minor configuration](/docs/proget/reference-api/proget-pgutil#sources) before use. This includes setting up your ProGet instance and API key as a source by running:

```bash
$ pgutil sources add --name=Default --url=«proget-server» --api-key=«api-key»
```

For example, adding the ProGet instance `https://proget.corp.local/` with the API Key `abc12345` you would enter:

```bash
$ pgutil sources add --name=Default --url=https://proget.corp.local/ --api-key=abc12345
```

Now upload your packages by entering:

```bash
$ pgutil packages upload --feed=internalized-chocolatey --input-file=«path-to-package»
```

For example, to upload the package `my-package-1.2.3.nupkg` stored at `C:\chocolatey_packages\` to your `internalized-chocolatey` feed you would enter:

```bash
$ pgutil packages upload --feed=internalized-chocolatey --input-file=C:\chocolatey_packages\my-package-1.2.3.nupkg
```

Your package will then be uploaded to the `internalized-chocolatey` feed.

![](){height="" width="50%"}

## Step 5: Adding ProGet as a Source { #add-source }

To add your `internalized-chocolatey` feed as a source in your client, use the `choco source add` command. We recommend giving the source the same name as your feed. For example, if adding the ProGet server `proget.corp.local` as a source, you would enter:

```bash
$ choco source add -n="internalized-chocolatey" -s="http://proget.corp.local/feeds/internalized-chocolatey" --priority=1 
```

To make sure that your client only installs your internalized packages, we recommend removing any default sources already configured using the `choco source remove` command:

```bash
$ choco source remove -n="chocolatey"
```
You can confirm your client is properly connected to ProGet using the `choco source list` command:

```bash
$ choco source list
```

## Step 5: (Optional) Authenticating to Your Chocolatey Feed

By default your `internalized-chocolatey` feed does not need to be authenticated to, and can be viewed and installed from anonymously. However, you may want to make your repository private and authenticate to it. You can use a username and password for this, but we strongly recommend using an [API Key](/docs/proget/reference-api/proget-apikeys), with `api` as the username, and the API Key as the password. To learn more about creating one, read [Authenticating to Chocolatey Feeds](/docs/proget/feeds/chocolatey#authenticating-to-a-chocolatey-feed). 