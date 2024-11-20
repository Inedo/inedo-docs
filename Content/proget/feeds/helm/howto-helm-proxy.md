---
title: "HOWTO: Proxy Helm Charts from Artifact Hub in ProGet"
order: 1
---

In ProGet, [Helm](https://helm.sh/) Charts can be stored and distributed through a Helm ["Feed"](/docs/proget/feeds/feed-overview), functioning as a [Chart Repository](https://helm.sh/docs/topics/chart_repository/) that can proxy third party [Charts](https://helm.sh/docs/topics/charts/) from other sources, including [Artifact Hub](/docs/proget/feeds/helm#artifacthub). This lets you to build a curated list of approved Helm Charts. You can also cache charts, allowing teams to access them if external sources are down, as well as tell which charts are being downloaded and used frequently.

This guide will walk you through setting up a ProGet feed as a Helm repository to proxy third-party charts. Additionally, we’ll cover how to create a private repository for your own internal charts.

## Step 1: Create a New Helm Feed { #create-feed }

First, we will create a Helm feed that will proxy charts from [Artifact Hub](/docs/proget/feeds/helm#artifacthub).

Start by selecting "Feeds" and "Create New Feed". Next, select "Helm Charts", under "Container-based Applications & Images"

![](){height="" width="50%"}

Now select "Connect to Artifact Hub". Then select "No, Create One Feed", as we will be creating a single feed to proxy Helm charts. From here, name the feed (we will call it `public-helm` for this guide). Then click "Create Feed".

![](){height="" width="50%"}

We are then presented with several options. Keeping these checked will allow your feed to use ProGet's [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) amd [Licensing Detection and Blocking](https://docs.inedo.com/docs/proget/sca/licenses) features. Select "Set Feed Features", which will create the feed, and redirect you to the newly created `public-helm` feed, now populated with charts proxied from Artifact Hub.

![](){height="" width="50%"}

## Step 2: Adding the Helm Feed as a Repository { #add-feed }

To install chart from your `public-helm` feed, it needs to be registered as a repository. You can use the [`helm repo add`](https://helm.sh/docs/helm/helm_repo_add/) command to do this:

```bash
$ helm repo add «repository-name» http://«proget-server»/helm/«feed-name»
```

We recommend using `proget` as the repository name or, if you have multiple Helm feeds configured, you should use `proget-«feed-name»` instead. For example, if adding your public helm feed on the ProGet server proget.corp.local, you would enter:
 
```bash
$ helm repo add proget http://proget.corp.local/helm/public-helm
```

## Step 3: (Optional) Authenticating to Your Helm Feed

By default your `public-helm` feed does not require authentication to install or view charts. However, you may want to make your feed private and require authentication to access. You can learn more about how to do this by reading [Authenticating Helm Feeds](/docs/proget/feeds/helm#authenticated-feeds)

## Step 4: Installing Helm Charts

Once you have configured your Helm feed as a repository, you can install Helm charts using the [`helm install`](https://helm.sh/docs/helm/helm_install/) command. 

```bash
helm install «repository-name»/«chart-name»
```

For example, installing the chart `MyChart` from a repository you have configured as `proget`, you would enter:

```bash
helm install proget/MyChart
```

Note that, because Helm caches repository information locally, you should generally run the [helm repo update](https://helm.sh/docs/helm/helm_repo_update/) command first to ensure you'll get the latest version of the chart.

## (Optional) Creating a Package Approval Flow

In this guide we looked at proxying charts from Artifact Hub. However, with no form of approval, developers will be able to install any OSS charts without any form of approval. It's important to include some form of oversight or approval, which can be done by creating a ["Package Approval Flow"](/docs/proget/packages/package-promotion).

To set up a package approval flow, refer to [HOWTO: Approve and Promote Open-source Packages](/docs/proget/packages/package-promotion/proget-howto-promote-packages). This guide uses NuGet feeds as an example, but the steps are identical when creating Helm chart feeds.

After creating your "Unapproved" and "Approved" feeds, follow the steps in ["Adding the Helm Feed as a Repository"](#add-feed) to add the "Approved" feed (e.g. `approved-helm`) as a repository in your Helm client using the `helm repo add` command:

```bash
$ helm repo add proget http://«proget-server»/helm/approved-helm
```


