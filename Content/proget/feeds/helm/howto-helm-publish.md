---
title: "HOWTO: Create and Publish Helm Charts to a Private Repository"
order: 2
---

ProGet allows you to easily set up Helm ["Feeds"](/docs/proget/feeds/feed-overview) as private repositories for your [Helm Charts](https://helm.sh/docs/topics/charts/), enabling you to publish, store, and share them internally.

This article will guide you through creating a feed in ProGet to serve as a private Helm [Chart Repository](https://helm.sh/docs/topics/chart_repository/). We'll also cover the steps to create, publish, and install Helm Charts from this feed.

## Step 1: Create a New Helm Feed

First, we will create a feed to host your Helm charts. Start by selecting "Feeds" and "Create New Feed". Next, select "Helm Charts".

![](){height="" width="50%"}

Then select "No Connectors (Private packages only)" as we will be creating a private feed. From here, we will give our feed a name. In this guide, we will call it `internal-helm`, and then click "Create Feed".

![](){height="" width="50%"}

You will then see several options. These relate to ProGet's [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) features, however they are only for users looking to use third party packages. Leave these boxes unchecked, and select [Set Feed Features]. You will then be redirected to your new `internal-npm` feed, currently empty.

![](/resources/docs/proget-npm-internal-empty.png){height="" width="50%"}

## Step 2: Create Your Helm Chart

There are no special requirements for creating Helm Charts in ProGet, but you will need to follow [image naming conventions in your Values file](#values-yaml) if you'd like ProGet to detect the container images that your chart references.

In general, we recommend creating simple charts from scratch instead of trying to modify or use a third-party chart you might find on Artifact Hub. 


This involves using the [helm create](https://helm.sh/docs/helm/helm_create/) command:

```bash
helm create «chart-name»
```

For example creating a chart my-chart, you would enter:

```bash
helm create my-chart
```

You will then need to modifying the neccessary files in the created directory. Finally run the [`helm package`](https://helm.sh/docs/helm/helm_package/) command to create a package file:

```bash
helm package «path-to-chart»
```

For example, creating version `1.15.3` of the package `my-chart` located in `C:/Users/Inedo/projects/helm-charts/` you would enter:

```bash
$ helm package C:/Users/Inedo/projects/helm-charts/mychart
```

This will create the package (e.g. `my-chart-1.15.3.tgz`) that can be published to your feed.

See the official [Chart Template Guide](https://helm.sh/docs/chart_template_guide/getting_started/) to learn how to get started with creating your own Helm Charts.

## Step 3: Create an API Key

Now create an [API Key](/docs/proget/reference-api/proget-apikeys) which will allow youto authenticate to the `internal-rpm` feed to publish Helm charts to it.

You can read more about creating API keys in ProGet on our [API Key](/docs/proget/reference-api/proget-apikeys) page.

When creating an API Key, fill in the fields by selecting "Feeds (Use Certain Feeds)" as the "Feed Type" and selecting the `internal-rpm` feed. Then set the API key. You can use any alphanumeric sequence, or just leave it blank to autogenerate one.

![](){height="" width="50%"}

Make sure the "View/Download" and "Add/Repackage" boxes are checked, and then select "Save".

## Step 4: Publish Your Helm Chart Package

The easiest way to push a Helm Chart to your feed is with [`pgutil packages upload`](/docs/proget/reference-api/proget-api-packages/proget-api-packages-upload) command:

```bash
$ pgutil packages upload --feed=«feed-name» --input-file=«path-to-chart-tgz»
```

If you're using ProGet 2023 or earlier, you can instead issue a `PUT` request with the package file as the content to the endpoint URL. For example:

```bash
curl https://«proget-server»/helm/«feed-name» --user api:«api-key» --upload-file «path-to-chart-tgz»
```

This method also works in ProGet 2024 and later, should you prefer to use that. Note that neither the `helm push` nor the  `helm-push` are compatible with ProGet feeds.

## Step 5: Adding the Helm Feed as a Repository

To install chart from your `internal-helm` feed, it needs to be registered as a repository. You can use the [`helm repo add`](https://helm.sh/docs/helm/helm_repo_add/) command to do this:

```bash
$ helm repo add «repository-name» http://«proget-server»/helm/«feed-name»
```

We recommend using `proget` as the repository name or, if you have multiple Helm feeds configured, you should use `proget-«feed-name»` instead. For example, if adding your internal helm feed on the ProGet server proget.corp.local, you would enter:
 
```bash
$ helm repo add proget http://proget.corp.local/helm/internal-helm
```

## (Optional) Authenticate to Your Helm Feed

By default your `internal-helm` feed does not require authentication to install or view charts. However, you may want to make your feed private and require authentication to access. You can learn more about how to do this by reading [Authenticating Helm Feeds](/docs/proget/feeds/helm#authenticated-feeds)