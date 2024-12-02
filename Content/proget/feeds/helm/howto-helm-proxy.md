---
title: "HOWTO: Proxy Helm Charts from ArtifactHub in ProGet"
order: 1
---

In ProGet, [Helm](https://helm.sh/) Charts can be stored and distributed through a Helm ["Feed"](/docs/proget/feeds/feed-overview), functioning as a [Chart Repository](https://helm.sh/docs/topics/chart_repository/) that can proxy third party [Charts](https://helm.sh/docs/topics/charts/) from other sources, including [Artifact Hub](/docs/proget/feeds/helm#artifacthub). This lets you to build a curated list of approved Helm Charts. You can also cache charts, allowing teams to access them if external sources are down, as well as tell which charts are being downloaded and used frequently.

This guide will walk you through setting up a ProGet feed as a Helm repository to proxy third-party charts from ArtifactHub. Additionally, we’ll cover how to create a private repository for your own internal charts.

## Step 1: Create a New Helm Feed { #create-feed }

First, we will create a Helm feed that will proxy charts from [Artifact Hub](/docs/proget/feeds/helm#artifacthub). Start by selecting "Feeds" and "Create New Feed". Next, select "Helm Charts", under "Container-based Applications & Images"

![](/resources/docs/proget-helm-newfeed.png){height="" width="50%"}

Now select "Connect to Artifact Hub" followed by "No, Create One Feed", as we will be creating a single feed to proxy Helm charts. From here, name the feed (we will call it `public-helm` for this guide). Then click "Create Feed".

![](/resources/docs/proget-helm-newfeed-public.png){height="" width="50%"}

We are then presented with several options that will let you configure ProGet's [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) and [Licensing Detection and Blocking](https://docs.inedo.com/docs/proget/sca/licenses) features. Select "Set Feed Features", which will create and redirect you to the newly created `public-helm` feed, now populated with charts proxied from Artifact Hub.

![](/resources/docs/proget-helm-public-populated.png){height="" width="50%"}

## Step 2: Adding the Helm Feed as a Repository { #add-feed }

To install chart from your `public-helm` feed, it needs to be registered as a repository. You can use the [`helm repo add`](https://helm.sh/docs/helm/helm_repo_add/) command to do this. 

It's important to note that Artifact Hub is not a Helm repository itself, but an aggregator of Helm repositories. This means that you cannot simply use your public-helm feed URL to add it as a repository. Instead you'll need to add a specific URL based on the repository name. 

Helm charts sourced from Artifact Hub are always labeled with the repository name as a prefix. For instance, the `kube-prometheus-stack` chart will be listed as `prometheus-community/kube-prometheus-stack` in ProGet.

![](/resources/docs/proget-helm-chart-name.png){height="" width="50%"}

To add these repositories, you'll need the URL found on the Helm chart overview page in ProGet, which will typically resemble the following:

![](/resources/docs/proget-helm-chart-url.png){height="" width="50%"}
 
For example, to add the `prometheus-community` repository to let you install the `kube-prometheus-stack` chart you would enter:

```bash
helm repo add prometheus-community http://proget.corp.local/helm/public-helm/prometheus-community
```

## Step 3: (Optional) Authenticating to Your Helm Feed

By default your `public-helm` feed does not require authentication to install or view charts. However, you may want to make your feed private and require authentication to access. You can learn more about how to do this by reading [Authenticating Helm Feeds](/docs/proget/feeds/helm#authenticated-feeds).

## Step 4: Installing Helm Charts

Once you have configured your `public-helm` feed as a repository, you can install Helm charts using the [`helm install`](https://helm.sh/docs/helm/helm_install/) command. 

When installing Helm Charts from Artifact Hub you will need to make sure that the [correct repository has been added](#add-artifact-hub). You will also need to include the version when using the `helm install` command. For example, when installing version `66.3.0` of the `kube-prometheus-stack` Helm chart you would enter:

```bash
helm install my-kube-prometheus-stack prometheus-community/kube-prometheus-stack --version 66.3.0
```

:::(info)
Note that `my-kube-prometheus-stack` corresponds to the release name, and can be changed to suit your needs. You can also add [additional flags](https://helm.sh/docs/helm/helm_install/#options) to the `helm install` command if you need to.
:::

As Helm caches repository information locally, we recommend running the [helm repo update](https://helm.sh/docs/helm/helm_repo_update/) command first to ensure you'll get the latest version of the chart.

## (Optional) Creating a Package Approval Flow

In this guide we looked at proxying charts from Artifact Hub. However, using the `public-helm` feed, developers can install any OSS charts without any form of oversight. To make sure that developers are only using charts suitable for your organization, you can introduce a ["Package Approval Flow"](/docs/proget/packages/package-promotion).

To set up a package approval flow, refer to [HOWTO: Approve and Promote Open-source Packages](/docs/proget/packages/package-promotion/proget-howto-promote-packages). The guide uses NuGet feeds as an example, but the steps are identical when creating Helm chart feeds.

After creating the feeds in your approval flow, follow the steps in ["Adding the Helm Feed as a Repository"](#add-feed) to add the "Approved" feed (e.g. `approved-helm`) as a repository in your Helm client using the `helm repo add` command. For example, if adding this feed on the ProGet server `proget.corp.local`, you would enter:

```bash
$ helm repo add proget http://proget.corp.local/helm/approved-helm
```


