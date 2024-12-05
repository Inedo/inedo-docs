---
title: "HOWTO: Create and Publish Helm Charts to a Private Repository"
order: 2
---

ProGet allows you to easily set up Helm ["Feeds"](/docs/proget/feeds/feed-overview) as private repositories for your [Helm Charts](https://helm.sh/docs/topics/charts/), enabling you to publish, store, and share them internally.

This page will take you through creating a feed in ProGet to serve as a private Helm [Chart Repository](https://helm.sh/docs/topics/chart_repository/). We'll also cover creating, publishing, and install Helm Charts from this feed.

## Step 1: Create a New Helm Feed

First, we will create a feed to host your Helm charts. Start by selecting "Feeds" and "Create New Feed". Next, select "Helm Charts".

![](/resources/docs/proget-helm-newfeed.png){height="" width="50%"}

Then select "No Connectors (Private packages only)" as we will be creating a private feed. From here, we will give our feed a name. In this guide, we will call it `internal-helm`, and then click "Create Feed".

![](/resources/docs/proget-helm-newfeed-internal.png){height="" width="50%"}

You will then see several options. These relate to ProGet's SCA features, useful for third party charts. As we will be using our own internal charts, we will leave these boxes unchecked, and select "Set Feed Features". You will then be redirected to your new `internal-helm` feed, currently empty.

![](/resources/docs/proget-helm-internal-empty.png){height="" width="50%"}

## Step 2: Create Your Helm Chart

There are no special requirements for creating Helm Charts in ProGet, but you will need to follow [image naming conventions in your Values file](/docs/proget/feeds/helm#values-yaml) if you'd like ProGet to detect the container images that your chart references.

In general, we recommend creating simple charts from scratch instead of trying to modify or use a third-party chart you might find on [Artifact Hub](/docs/proget/feeds/helm#artifacthub). 

This involves using the [helm create](https://helm.sh/docs/helm/helm_create/) command. In the case of creating a chart `my-chart`, you would enter:

```bash
helm create my-chart
```

You will then need to modifying the necessary files in the created directory. Finally run the [`helm package`](https://helm.sh/docs/helm/helm_package/) command to create a package file. For example, creating version `1.15.3` of the package `my-chart` located in `./projects/helm-charts/mychart` you would enter:

```bash
$ helm package ./projects/helm-charts/mychart
```

This will create the package (e.g. `my-chart-1.15.3.tgz`) that can be published to your feed. See the official [Chart Template Guide](https://helm.sh/docs/chart_template_guide/getting_started/) to learn how to get started with creating your own Helm Charts.

## Step 3: Create an API Key

Now create an [API Key](/docs/proget/reference-api/proget-apikeys) which will allow you to authenticate to the `internal-helm` feed to publish Helm charts to it. You can read more about creating API keys in ProGet on our [API Key](/docs/proget/reference-api/proget-apikeys) page.

When creating an API Key, Select "Feeds (Use Certain Feeds)" as the "Feed Type" and select the `internal-helm` feed. When setting the API key you can either use any alphanumeric sequence, or just leave it blank for ProGet to autogenerate one for you.

![](/resources/docs/proget-helm-api-key.png){height="" width="50%"}

Make sure the "View/Download" and "Add/Repackage" boxes are checked, and then select "Save".

## Step 4: Publish Your Helm Chart

To publish your Helm chart to your `internal-helm` feed, you can use [pgutil](/docs/proget/reference-api/proget-pgutil). This will require some [minor configuration](/docs/proget/reference-api/proget-pgutil#sources) before use. This includes setting up your ProGet instance and API key as a source by running the `sources add` command. For example, adding the ProGet instance `https://proget.corp.local/` with the API Key `abc12345` you would enter:

```bash
$ pgutil sources add --name=Default --url=https://proget.corp.local/ --api-key=abc12345
```

Now upload your charts by using the [`pgutil packages upload`](/docs/proget/reference-api/proget-api-packages/proget-api-packages-upload) command:

```bash
$ pgutil packages upload --feed=internal-helm --input-file=./projects/helm-charts/mychart
```

If you're using ProGet 2023 or earlier, you can instead issue a `PUT` request with the package file as the content to the endpoint URL. For example:

```bash
curl https://proget.corp.local//helm/internal-helm --user api:abc12345 --upload-file ./projects/helm-charts/mychart
```

This method also works in ProGet 2024 and later, should you prefer to use that. Note that neither the `helm push` nor the  `helm-push` are compatible with ProGet feeds.

## Step 5: Adding the Helm Feed as a Repository

To install chart from your `internal-helm` feed, it needs to be registered as a repository. You can use the [`helm repo add`](https://helm.sh/docs/helm/helm_repo_add/) command to do this. We recommend using `proget` as the repository name or, if you have multiple Helm feeds configured, you should use `proget-«feed-name»` instead. For example, if adding your `internal helm` feed on the ProGet server `proget.corp.local`, you would enter:
 
```bash
$ helm repo add proget http://proget.corp.local/helm/internal-helm
```

## (Optional) Authenticate to Your Helm Feed

By default your `internal-helm` feed does not require authentication to install or view charts. However, you may want to make your feed private and require authentication to access. You can learn more about how to do this by reading [Authenticating Helm Feeds](/docs/proget/feeds/helm#authenticated-feeds)