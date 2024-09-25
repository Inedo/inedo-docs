---
title: "HOWTO: Proxy OSS Conda Packages from Anaconda Packages"
order: 1
---

With ProGet teams can proxy packages from the [Anaconda OSS Package Repository](https://repo.anaconda.com/) and consume them in their projects as they would if they were pulling from the OSS package repository directly. 

The advantages of using ProGet for this are:
* ProGet will cache packages allowing teams to access them even when the Anaconda Packages repository is down
* ProGet will show which packages are being downloaded and used frequently

In this article, we'll explain how to create a ["Feed"](/docs/proget/feeds/feed-overview) in ProGet that will proxy Conda packages from the public OSS Repository. 

We'll also look at creating a private repository for when you also want to use internal packages, and how to create a package approval flow if you need to control which packages your team are using in production. 

## Step 1: Create a New Feed { #step-1 }

First, we will create a Conda feed that will proxy packages from [Anaconda OSS Package Repository](https://repo.anaconda.com/).

Start by selecting "Feeds" and "Create New Feed".

![New Feed](/resources/docs/proget-feeds-createnewfeed.png){height="" width="50%"}

Next, select "Conda Packages", as we will be creating feeds to proxy and host Conda packages.

![Select Conda](/resources/docs/proget-conda-newfeed.png){height="" width="50%"}

Now select "Connect to Anaconda Packages" which will allow us to proxy packages from the [Anaconda OSS Package Repository](https://repo.anaconda.com/)

![Connector](/resources/docs/proget-conda-newfeed-connector.png){height="" width="50%"}

Then select "No, Create One Feed", as we will be creating a single feed to proxy Conda packages.

![One Feeds](/resources/docs/proget-conda-onefeed.png){height="" width="50%"}

## Step 2: Name Your Feed

From here, we name our feed, which in this example we will call `public-conda`. Then click "Create Feed".

![Name Feed](/resources/docs/proget-conda-onefeed-name.png){height="" width="50%"}

We are then presented with several options. More information on these can be found in the [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) documentation.

![SCA Features](/resources/docs/proget-conda-newfeed-sca.png){height="" width="50%"}

Finally, we select [Set Feed Features], which will create the feed, and redirect us to our `public-conda` feed, now populated with packages proxied from the public OSS Conda repository.

![Feed](/resources/docs/proget-conda-publicfeed.png){height="" width="50%"}

## Step 3: Adding the Feed to Local Conda Environments { #step-3 }

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

## Step 4: (Optional) Confirming Connection to your Conda Feed

You can confirm that your local Conda environment can connect with your ProGet feed by listing Conda packages from the feed by entering:

```bash
$ conda search -c «feed-url»
```

Or by filtering by package name:

```bash
$ conda search -c «feed-url» «package-name»
```

## (Optional) Authenticating to Your Conda Feed

By default your `public-conda` feed will not require authentication and can be viewed anonymously. However you may want to make your repository private and require authentication to access it. This is recommended when hosting your own internal packages in a feed, in addition to proxied packages from the OSS repository. 

First navigate to navigate to "Settings"> "Manage Security", and remove anonymous access by clicking the small "X" in the "Anonymous" entry. 

![Permissions Remove](/resources/docs/proget-conda-permissions-remove.png){height="" width="50%"}

Now you will need to create an [API Key](/docs/proget/reference-api/proget-apikeys). 

Start by navigating to "Administration Overview" > "API Keys & Access Logs" under "Security & Authentication"

![Admin Overview](/resources/docs/proget-admin-apikeys.png){height="" width="50%"}

Then select "Create API Key"

![Create Key](/resources/docs/proget-apikey-new.png){height="" width="50%"}

Then fill in the fields by selecting "Feeds ("Use Certain Feeds)" as the "Feed Type" and selecting the `public-conda` feed. Then set the API key. You can specify any alphanumeric sequence for this, or leave it blank to autogenerate one.

Ensure that the "View/Download" box is checked, and then select "Save".

![API Key](/resources/docs/proget-conda-apikey-3.png){height="" width="50%"}

Now, we'll add the feed to a local Conda environment. Instead of adding the URL like in [Step 3](#step-3), enter the following, containing both URL and API Key:

```bash
$ conda config --add channels http://api:«api-key»@«feed-url»
```

For example, when authenticating with the API key abc12345 to the URL `http://proget.corp.local/conda/public-conda/` you would enter:

```bash
$ conda config --add channels http://api:abc12345@proget.corp.local/conda/public-conda/
```

Confirm that it was registered by entering:

```bash
$ conda config --show channels
```

## (Optional) Creating a Package Approval Flow

So far we've looked at proxying packages from an OSS repository. However you may want to make sure that only approved packages are used in your development or production environment to avoid risks related to quality, vulnerabilities, licenses, etc.

In ProGet, you can create a "Package Approval Flow" that involves promoting packages between feeds to ensure that only approved and verified packages are used in the right environments, such as production. You can read more about package promotion [in our documentation](/docs/proget/packages/package-promotion).

To configure package approval flow you can read [HOWTO: Approve and Promote Open-source Packages](/docs/proget/packages/package-promotion/proget-howto-promote-packages). In this guide it talks about setting up a NuGet package approval, but it can just as easily be done with Conda packages by creating Conda feeds instead. 

Once you have created your "Unapproved" and "Approved" feeds, follow the steps in [Step 3](#step-3) to add the "Approved" feed as a channel to your local Conda environments, entering:

```bash
$ conda config --add channels «feed-url»
```

To ensure that developers only consume packages from the "Approved" feed rather than the OSS repository, we recommend removing the `defaults` channel, which exists by default. This can be done by entering:

```bash
$ conda config --remove channels defaults
```





