---
title: "HOWTO: Proxy Chocolatey Packages from Chocolatey.org to a Private Repository"
order: 1
---

ProGet can be set up to proxy Chocolatey packages from [Chocolatey.org](https://chocolatey.org), letting organizations install these packages just as they would if installing them directly using `choco install`. 

In this guide, we'll walk you through creating a "Feed" in ProGet to proxy packages from the Chocolatey repository. We'll also cover how to configure a private Chocolatey repository for internal packages, and internalize packages to let your Chocolatey packages be used in air gapped or offline environments. 

## Step 1: Create a Chocolatey Feed

We'll first create a Chocolatey feed to proxy packages from [Chocolatey.org](https://chocolatey.org). You can read more on creating feeds by reading [Creating and Managing Feeds](/docs/proget/feeds/feed-overview#creating-and-managing-feeds)

In your ProGet instance, select "Feeds" and "Create New Feed", and select "Chocolatey Packages".

![](){height="" width="50%"}

Then select "Connect to Chocolatey.org" which will let you proxy and cache packages from Chocolatey.org.

![](){height="" width="50%"}

Select "No, Create One Feed", as we will only need a single feed to proxy Chocolatey packages. Now you will need to name the feed. For this example, we will name it `public-chocolatey`, and then click "Create Feed".

![](){height="" width="50%"}

You'll then choose from several vulnerability and license options. More information on these can be found in the [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) and [Licensing Detection and Blocking](https://docs.inedo.com/docs/proget/sca/licenses) documentation. Select "Set Feed Features". ProGet will create The `public-chocolatey` feed, which will now be populated with packages proxied from Chocolatey.org.

![](){height="" width="50%"}

## Step 2: (Optional) Enable Metadata Caching

Although not required, we recommend enabling metadata caching, which will significantly reduce the number of times ProGet must forward queries to "chocolatey" and avoid rate limiting. To enable metadata caching, navigate to "Feeds" > "Connectors", and select "chocolatey.org".

![Feed Connectors "feed-connectors"](/resources/docs/proget-feed-chocolatey-connectors.png){height="" width="50%"}

From here, locate "Metadata Caching" on the right side, and select "edit". This will bring up a dialog box. Click the checkbox next to "Cache metadata queries". Doing so will bring up two options, "Cache period (minutes)" and "Minimum cache entries" that can be set at your discretion. We recommend keeping them to the default values.

![Metadata Caching "metadata-caching"](/resources/docs/proget-configuremetadatacaching.png){height="" width="50%"}

Finally, click "Save". The changes will now be reflected on the "Connectors" page.

![Caching Enabled "caching-enabled"](/resources/docs/proget-chocolatey-cachingenabled.png){height="" width="50%"}

## Step 3: Adding ProGet As A Source  { #add-source }

To add your `public-chocolatey` feed as a source in your client, use the `choco source add` command. We recommend giving the source the same name as your feed. For example, if adding the ProGet server `proget.corp.local` as a source, you would enter:

```bash
$ choco source add -n="public-chocolatey" -s="http://proget.corp.local/feeds/public-chocolatey" --priority=1 
```

To make sure that your client only installs Chocolatey Packages from ProGet, we recommend removing any default sources already configured using the `choco source remove` command:

```bash
$ choco source remove -n="chocolatey"
```
You can confirm your client is properly connected to ProGet using the `choco source list` command:

```bash
$ choco source list
```

## Step 4: Installing Chocolatey Packages

Once you have created your `public-chocolatey` feed and configured it as a source in your client, you can install packages using the `choco install` command. For example, if you were installing version `1.2.3` of `MyApplication`, you would enter:

```bash
$ choco install MyApplication --version 1.2.3 --source public-chocolatey
```

## Step 5: (Optional) Authenticating to Your Chocolatey Feed

By default your `public-chocolatey` feed does not need to be authenticated to, and can be viewed and installed from anonymously. However, you may want to make your repository private and authenticate to it. You can use a username and password for this, but we strongly recommend using an [API Key](/docs/proget/reference-api/proget-apikeys), with `api` as the username, and the API Key as the password. To learn more about creating one, read [Authenticating to Chocolatey Feeds](/docs/proget/feeds/chocolatey#authenticating-to-a-chocolatey-feed). 

## (Optional) Creating a Package Approval Flow

In this guide, we covered how to proxy packages from [Chocolatey.org](https://www.Chocolatey.org/). However, this lets uses install any package straight from this open-source repository without any oversight. In organizations, it's recommended that you include some form of approval to make sure only compliant packages are used.

This can be solved by introducing a ["Package Approval Flow"](/docs/proget/packages/package-promotion). To set up a package approval flow, refer to [HOWTO: Approve and Promote Open-source Packages](/docs/proget/packages/package-promotion/proget-howto-promote-packages). 

After creating your "Unapproved" and "Approved" feeds, add your "Approved" feed (e.g. `approved-chocolatey`) as a source with the chocolatey sources add command:

```bash
$ choco source add -n="approved-chocolatey" -s="https://«proget-server»/feeds/approved-chocolatey" --priority=1 
```

## (Optional) Internalizing Chocolatey Packages

If you want to minimizing reliance on external sources for software installed using Chocolatey Packages, you can [internalize your packages](https://blog.inedo.com/chocolatey/internalization). Chocolatey’s [package internalizer](https://docs.chocolatey.org/en-us/features/package-internalizer) can internalize packages automatically or you can do it yourself. To learn more on how to do this you can read [HOWTO: Set Up a Private Chocolatey Repository for Internalized Packagesy](/docs/proget/feeds/chocolatey/howto-chocolatey-internalized)