---
title: "HOWTO: Proxy Packages from the npm Registry in ProGet"
order: 1
---

With ProGet you can create ["Feeds"](/docs/proget/feeds/feed-overview) to proxy packages from the npm Registry([www.npmjs.com](https://www.npmjs.com/)) and install them just as you would when installing them from the npm Registry directly. 

Using ProGet as a proxy will cache packages, allowing teams to access them even if the npm Registry is down. ProGet will also tell you which packages are being downloaded and used frequently

This guide will cover how to set up a feed to proxy packages. We'll also cover how to create a private repository for your internal packages as an alternative to the npm Registry's paid private repositories (npm Teams/npm Pro).

## Step 1: Create a New Feed { #step-1 }

First, we will create an npm feed that will proxy packages from the [www.npmjs.com](https://www.npmjs.com/).

Start by selecting "Feeds" and "Create New Feed". Next, select "npm Packages", as we will be creating feeds to proxy and host npm packages.

![](){height="" width="50%"}

Now select "Connect to npmJS.org" which will allow us to proxy packages from the npm Registry.

![](){height="" width="50%"}

Then select "No, Create One Feed", as we will be creating a single feed to proxy npm packages. From here, name the feed (we will call it `public-npm` for this guide). Then click "Create Feed".

![](){height="" width="50%"}

We are then presented with several options. More information on these can be found in the [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) documentation. Select "Set Feed Features", which will create the feed, and redirect you to the newly created `public-npm` feed, now populated with packages proxied from the npm Registry.

![](){height="" width="50%"}

## Step 3: Add the Feed to npm Clients { #add-feed }

For your team to install packages from the `public-npm` feed, you'll need to add it as a source in their npm clients. For this, you will need feed's URL. This can be found at the top right of the feed's page.

![](){height="" width="50%"}

Now configure your npm client with your `public-npm` feed by entering: 

```bash
$ npm config set registry http://«proget-url»/npm/public-npm
```

You can confirm that the `public-npm` feed has been set correctly by entering:

```bash
$ npm get registry
```

## (Optional) Authenticating to Your npm Feed

By default your `public-npm` feed does not require authentication and can be viewed anonymously. However, you may want to make your feed private and require authentication] to access [by creating an `_auth` token](/docs/proget/feeds/npm#authenticating-to-npm-feeds). This is a username and password string `«username»:«password»` that's been base64-encoded. 

## (Optional) Creating a Package Approval Flow

In this guide we looked at proxying packages from the npm Registry. However, with no form of approval, developers will be able to install any OSS packages without oversight. In many cases, it's important to include some form of oversight in development or production, which can be done by creating a ["Package Approval Flow"](/docs/proget/packages/package-promotion).

To set up a package approval flow, refer to [HOWTO: Approve and Promote Open-source Packages](/docs/proget/packages/package-promotion/proget-howto-promote-packages). This guide uses NuGet feeds as an example, but the steps are identical when creating npm package feeds.

After creating your "Unapproved" and "Approved" feeds, follow the steps in ["Add the Feed to npm Cients"](#add-feed) to add the "Approved" feed (e.g. `npm-approved`) as a source in your npm client, entering:

```bash
$ npm config set registry http://«proget-url»/npm/npm-approved
```
