---
title: "HOWTO: Publish npm Packages to a Private Repository in ProGet"
order: 2
---

ProGet lets you easily set up private repositories for your [npm](https://www.npmjs.com/) packages, so you can publish, store, and share them internally.

This article will show you how to create a ["Feed"](/docs/proget/feeds/feed-overview) in ProGet to act as a private npm package repository. We'll also look at how to create, publish, and install packages from this feed.

## Step 1: Create a New Feed

First, we will create a feed to host your npm packages. Start by selecting "Feeds" and "Create New Feed". Next, select "npm Packages".

![](){height="" width="50%"}

Then select "No Connectors (Private packages only)" as we will be creating a private feed. From here, we will give our feed a name. For this article, we will call it `internal-npm`, and then click "Create Feed".

![](){height="" width="50%"}

We are then presented with several options. This relate to ProGet's [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) features, however they are only for users looking to use third party packages. Leave these boxes unchecked, and select [Set Feed Features]. You will then be redirected to your new `internal-npm` feed, currently empty.

![](){height="" width="50%"}

## Step 2: Create an API Key { #step-2 }

Now you'll need an [API Key](/docs/proget/reference-api/proget-apikeys) to allow your local client to authenticate to your `internal-npm` and publish packages to it, as well as install them once they are in the feed.

Start by navigating to "Administration Overview" > "API Keys & Access Logs" under "Security & Authentication"

![Admin Overview](/resources/docs/proget-admin-apikeys.png){height="" width="50%"}

Then select "Create API Key"

![Create Key](/resources/docs/proget-apikey-new.png){height="" width="50%"}

Then fill in the fields by selecting "Feeds (Use Certain Feeds)" as the "Feed Type" and selecting the `internal-npm` feed. Then set the API key. You can specify any alphanumeric sequence for this, or leave it blank to autogenerate one. Check the "View/Download" and "Add/Repackage" boxes, and select "Save".

![New Key](/resources/docs/proget-conda-apikey-2.png){height="" width="50%"}

## Step 3: Build Your Package

### Scoping Your Packages

While not necessary for private packages, we recommend scoping the packages you create. 


### Step 4: Publish Your Package to ProGet

To publish your package to your `internal-npm` feed, you will need to run the npm publish command by entering:

```bash
$ npm publish
```

## Step 5: Add the Feed to Your Local npm Environment

To install npm packages that have been published to your `internal-npm` feed, you'll need to add it to your local npm environment. 

## Step 6: (Optional) Confirm Connection to your npm Feed

You can confirm that your `internal-npm` feed has been added to your local npm environment by entering the following command:

```bash
$ 
```

You can also list the packages in your feed by entering:

```bash
$ 
```
