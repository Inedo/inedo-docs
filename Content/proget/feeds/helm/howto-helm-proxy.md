---
title: "HOWTO: Proxy Helm Charts from Artifact Hub in ProGet"
order: 1
---

In ProGet, [Helm](https://helm.sh/) Charts can be stored and distributed through a Helm feed, functioning as a [Chart Repository](https://helm.sh/docs/topics/chart_repository/) that can proxy third party [Charts](https://helm.sh/docs/topics/charts/) from other sources, including [Artifact Hub](/docs/proget/feeds/helm#artifacthub). This lets you to build a curated list of approved Helm Charts. You can also cache charts, allowing teams to access them if external sources are down, as well as tell which charts are being downloaded and used frequently.

This guide will walk you through setting up a ProGet ["Feeds"](/docs/proget/feeds/feed-overview) as a Helm repository to proxy third-party charts. Additionally, we’ll cover how to create a private repository for your own internal charts.

## Step 1: Create a New Feed { #create-feed }

First, we will create an npm feed that will proxy charts from [Artifact Hub](/docs/proget/feeds/helm#artifacthub).

Start by selecting "Feeds" and "Create New Feed". Next, select "npm Packages", as we will be creating feeds to proxy and host npm packages.

![](/resources/docs/proget-npm-createfeed.png){height="" width="50%"}

Now select "Connect to npmJS.org" which will allow us to proxy packages from the npm Registry.

![](/resources/docs/proget-npm-connectors.png){height="" width="50%"}

Then select "No, Create One Feed", as we will be creating a single feed to proxy npm packages. From here, name the feed (we will call it `public-npm` for this guide). Then click "Create Feed".

![](/resources/docs/proget-npm-public-name.png){height="" width="50%"}

We are then presented with several options. Keeping these checked will allow your feed to use ProGet's [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) amd [Licensing Detection and Blocking](https://docs.inedo.com/docs/proget/sca/licenses) features. This will allow you to [use `npm audit` to scan packages](#scan-feed) for vulnerabilities. Select "Set Feed Features", which will create the feed, and redirect you to the newly created `public-npm` feed, now populated with packages proxied from the npm Registry.

![](/resources/docs/proget-npm-public.png){height="" width="50%"}

## Step 3: Add the Feed to npm Clients { #add-feed }

For your team to install packages from the `public-npm` feed, you'll need to add it as a source in their npm clients. For this, you will need feed's URL. This can be found at the top right of the feed's page.

![](/resources/docs/proget-npm-public-url.png){height="" width="50%"}

Now configure your npm client with your `public-npm` feed with `npm config`: 

### Using npm

```bash
$ npm config set registry http://«proget-url»/npm/«feed-name»
```

Or in Yarn with `yarn config`:

```bash
$ yarn config set registry http://«proget-url»/npm/«feed-name»
```

You can confirm that the feed has been set correctly by using `npm get registry` for npm, `yarn config get registry` for Yarn.

## (Optional) Authenticating to Your npm Feed

By default your `public-npm` feed does not require authentication and can be viewed anonymously. However, you may want to make your feed private and require authentication to access. Authentication is done by creating an `_auth` token and then [configuring it in your npm client](/docs/proget/feeds/npm#authenticating-to-npm-feeds).

## (Optional) Auditing npm Packages { #scan-feed }

If you enabled "Scan for Security Vulnerabilities" when you [created a feed](#create-feed) you can use npm-audit to scan for vulnerabilities in packages by simply running either `npm audit` or `yarn audit`.

Using `npm audit` with [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities), lets you assess vulnerabilities in packages, and how they impact your organization.

You can also set up [Policies & Compliance Rules](https://docs.inedo.com/docs/proget/sca/policies) to create rules for vulnerabilities, licenses, deprecated packages, etc. Setting these up lets you block any packages that are considered "noncompliant".

## (Optional) Creating a Package Approval Flow

In this guide we looked at proxying packages from the npm Registry. However, with no form of approval, developers will be able to install any OSS packages without oversight. In many cases, it's important to include some form of oversight in development or production, which can be done by creating a ["Package Approval Flow"](/docs/proget/packages/package-promotion).

To set up a package approval flow, refer to [HOWTO: Approve and Promote Open-source Packages](/docs/proget/packages/package-promotion/proget-howto-promote-packages). This guide uses NuGet feeds as an example, but the steps are identical when creating npm package feeds.

After creating your "Unapproved" and "Approved" feeds, follow the steps in ["Add the Feed to npm Cients"](#add-feed) to add the "Approved" feed (e.g. `npm-approved`) as a source in your npm client using `npm config`:

```bash
$ npm config set registry http://«proget-url»/npm/npm-approved
```

Or in Yarn with `yarn config`:

```bash
$ yarn config set registry http://«proget-url»/npm/npm-approved
```
