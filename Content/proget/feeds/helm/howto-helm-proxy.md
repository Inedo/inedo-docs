---
title: "HOWTO: Proxy Helm Charts from ArtifactHub in ProGet"
order: 1
---

In ProGet, [Helm](https://helm.sh/) Charts can be stored and distributed through a Helm ["Feed"](/docs/proget/feeds/feed-overview), functioning as a [Chart Repository](https://helm.sh/docs/topics/chart_repository/) that can proxy third party [Charts](https://helm.sh/docs/topics/charts/) from either Helm repositories or the [Artifact Hub](/docs/proget/feeds/helm#artifacthub). This lets you to build a curated list of approved Helm Charts. You can also cache charts, allowing teams to access them if external sources are down, as well as tell which charts are being downloaded and used frequently.

This guide will walk you through setting up a ProGet feed as a Helm repository to proxy third-party charts from repositories aggregated in the Artifact Hub. Additionally, we’ll cover how to create a private repository for your own internal charts.

## Step 1: Create a New Helm Feed { #create-feed }

First, we will create a Helm feed that will be used to proxy Helm charts. Start by selecting "Feeds" and "Create New Feed". Next, select "Helm Charts", under "Container-based Applications & Images"

![Create Helm Feed](/resources/docs/proget-helm-newfeed.png){height="" width="50%"}

Now select "Free/Open Source Helm Charts" to cache and filter charts from Artifact Hub.

![Create Helm Connector](/resources/docs/proget-helm-connecttoorg.png){height="" width="50%"}

From here, we can name our feed. In this example, we'll call ours `public-helm`. Then select "Create New Feed".

![Name Helm Feed](/resources/docs/proget-helm-namepublicfeed.png){height="" width="50%"}

Then ProGet will redirect you to your newly created `public-helm` feed, now listing Helm Charts that can be proxied through the Artifact Hub.

![Public Helm Feed](/resources/docs/proget-helm-publicfeed.png){height="" width="50%"}

## Step 2: (Optional) Authenticating to Your Helm Feed

By default your `public-helm` feed does not require authentication to install or view charts. However, you may want to make your feed private and require authentication to access. You can learn more about how to do this by reading [Authenticating Helm Feeds](/docs/proget/feeds/helm#authenticated-feeds).

## Step 3: Installing Helm Charts

To install chart from your `public-helm` feed, you will need to add it's corresponding repository. Artifact Hub is not a Helm repository itself, but an aggregator of Helm repositories. This means that you cannot simply add your `public-helm` feed a repository. To list Helm charts in a feed, ProGet uses a [special API](https://artifacthub.io/docs/api/) to search and download charts, and list their specific repository. To install a chart you'll need to add a specific URL based on a Helm chart's repository name. 

### Adding the Helm Feed as a Repository { #add-feed }

Helm charts sourced from Artifact Hub are always labeled with the repository name as a prefix. For instance, the `kube-prometheus-stack` chart will be listed as `prometheus-community/kube-prometheus-stack` in ProGet.

![Chart Name](/resources/docs/proget-helm-chartname.png){height="" width="50%"}

To add these repositories, you'll need the URL found on the Helm chart overview page in ProGet, which will typically resemble the following:

![Chart URL](/resources/docs/proget-helm-charturl.png){height="" width="50%"}
 
To add a repository, you can use the [`helm repo add`](https://helm.sh/docs/helm/helm_repo_add/) command. For example, to add the `prometheus-community` repository to let you install the `kube-prometheus-stack` chart you would enter:

```bash
helm repo add prometheus-community http://proget.corp.local/helm/public-helm/prometheus-community
```

### Installing the Helm Chart

Once you have added the relevant repository, you can install Helm charts using the [`helm install`](https://helm.sh/docs/helm/helm_install/) command. 

When installing Helm Charts from Artifact Hub you will also need to include the version when using the `helm install` command. For example, when installing version `66.3.0` of the `kube-prometheus-stack` Helm chart you would enter:

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


