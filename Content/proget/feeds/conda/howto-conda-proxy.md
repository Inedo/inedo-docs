---
title: "HOWTO: Proxy OSS Conda Packages from Public Repositories"
order: 1
---

With ProGet teams can proxy packages from public repositories such as [Anaconda Packages](https://repo.anaconda.com/) or [Conda Forge](https://conda.anaconda.org/conda-forge) and consume them in their projects as they would if they were pulling from the OSS package repository directly. 

The advantages of using ProGet for this are:
* ProGet will cache packages allowing teams to access them even when the Anaconda Packages repository is down.
* ProGet will show which packages are being downloaded and used frequently.

In this article, we'll explain how to create a ["Feed"](/docs/proget/feeds/feed-overview) in ProGet that will proxy Conda packages from the public OSS Repository. 

We'll also look at creating a private repository for when you also want to use internal packages, and how to create a package approval flow if you need to control which packages your team are using in production. 

## Step 1: Create a New Feed

First, we will create a Conda feed that will proxy packages from [Anaconda OSS Package Repository](https://repo.anaconda.com/).

Start by selecting "Feeds" and "Create New Feed". Next, select "Conda Packages", as we will be creating feeds to proxy and host Conda packages.

![Select Conda Packages](/resources/docs/proget-conda-newfeed.png){height="" width="50%"}

Now select "Free/Open Source Conda Packages" which will allow us to proxy packages from the [Anaconda OSS Package Repository](https://repo.anaconda.com/).

![Create Connector](/resources/docs/proget-conda-newfeed-connector.png){height="" width="50%"}

From here, we can name our feed, which in this example we will call `public-conda`. Then select "Create Feed".

![Name Conda Feed](/resources/docs/proget-conda-ossfeed-name.png){height="" width="50%"}

ProGet will then redirect us to our `public-conda` feed, now populated with packages proxied from the public OSS Conda repository.

![Public Conda Feed](/resources/docs/proget-conda-publicfeed.png){height="" width="50%"}

### Step 2: (Optional) Changing your Connector to Conda-Forge

By default ProGet creates a connector to [Anaconda Packages](https://repo.anaconda.com/) when creating a new Conda feed. If you need to proxy Conda packages from [Conda Forge](https://conda.anaconda.org/conda-forge) instead, navigate to "Feeds" > "Connectors" and select the `repo.anaconda.com` connector.

![](/resources/docs/proget-connectors-conda.png){height="" width="50%"}

Then select "edit" under "Basic Properties". Replace the URL in the "Connector URL" field with `https://conda.anaconda.org/conda-forge`. We also recommend changing the name to something appropriate such as `conda.anaconda.org/conda-forge`. Then select "Save".

![](/resources/docs/proget-connectors-conda-edit.png){height="" width="50%"}

Note this it may take a short time for the local index to update. You can see the status under "Local Index" on the connector's page.

## Step 3: Adding the Feed to Local Conda Environments

For your team to consume packages from the `public-conda` feed, you'll need to add it as a channel in their local environment. For this, you will need feed's URL. This can be found at the top right of the feed's page.

![Feed](/resources/docs/proget-conda-publicfeed-url.png){height="" width="50%"}

In your terminal of choice, enter the following, which will require the `public-conda` feed URL:

```bash
$ conda config --add channels «feed-url»
```

For example, adding a feed with the URL `http://proget.corp.local/conda/public-conda/` you would enter:

```bash
$ conda config --add channels http://proget.corp.local/conda/public-conda
```

You can confirm that it was registered by entering:

```bash
$ conda config --show channels
```

Finally, to ensure that developers consume packages from the `public-conda` feed rather than the OSS repository, we recommend removing the `defaults` channel, which exists by default. This can be done by entering:

```bash
$ conda config --remove channels defaults
```

You can confirm that your local Conda environment can connect with your ProGet feed by listing Conda packages from the feed by entering:

```bash
$ conda search -c «feed-url»
```

Or by filtering by package name:

```bash
$ conda search -c «feed-url» «package-name»
```

To install packages from your public-conda feed, enter:

```bash
$ conda install «package-name»
```

## (Optional) Authenticating to Your Conda Feed

By default your `public-conda` feed does not require authentication and can be viewed anonymously. However, you may want to make your feed private and [configure it to require authentication](/docs/proget/feeds/rpm#authenticating-to-conda-feeds) to access. For example, when also hosting your own internal packages.

## (Optional) Creating a Package Approval Flow

So far we've looked at proxying packages from OSS repositories. However, this leaves developers free to install any OSS packages in the repository. In many cases, it's important to include some form of oversight in development, which can be done by introducing [Package Approval](/docs/proget/packages/package-promotion).

To configure package approval flow you can read [HOWTO: Approve and Promote Open-source Packages](/docs/proget/packages/package-promotion/proget-howto-promote-packages). In this guide it talks about setting up a NuGet package approval, but it can just as easily be done with Conda packages by creating Conda feeds instead. 

After creating your "Unapproved" and "Approved" feeds, follow the earlier steps to add the "Approved" feed as a source in your local rpm environments, entering:

```bash
$ conda config --add channels «feed-url»
```

To ensure that developers only consume packages from the "Approved" feed rather than the OSS repository, we recommend removing the `defaults` channel, which exists by default. This can be done by entering:

```bash
$ conda config --remove channels defaults
```





