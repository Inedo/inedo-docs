---
title: "HOWTO: Proxy Packages from Debian OSS Repositories"
order: 1
---

ProGet can be configured to proxy Debian packages from public OSS repositories, such as [http://ftp.debian.org](http://ftp.debian.org), which host various Debian distributions and their respective package archives. This will let teams install Debian packages as if they were pulling them directly from the configured repositories. These proxied packages can also be pulled and cached locally to your ProGet instance. This is useful when the particular mirror you are using is experiencing issues, or when you want to easily see which packages are being downloaded and used frequently.

In this article, we'll explain how to create a ["Feed"](/docs/proget/feeds/feed-overview) in ProGet that will proxy packages from Debian repositories you have configured using a ["Connector"](/docs/proget/feeds/connector-overview). 

We’ll also discuss how to set up a private repository for using internal Debian packages, as well as how to set up a package approval process to manage which packages your team can use in development.

## Step 1: Create a Debian Feed

To proxy Debian packages we will start by creating a feed in ProGet. You can read more on creating feeds by reading [Creating and Managing Feeds](/docs/proget/feeds/feed-overview#creating-and-managing-feeds)

In your ProGet instance, select "Feeds" and "Create New Feed", and select "Debian Packages" under "System & Software Configuration".

![](){height="" width="50%"}

From here, name the feed. In this example, we will call our feed `public-debian`. After entering the name, click "Create Feed".

![](/resources/docs/proget-cran-publicfeed.png){height="" width="50%"}

ProGet will create The `public-debian` feed and redirect you to it, currently empty.

![](/resources/docs/proget-cran-feed.png){height="" width="50%"}

## Step 2: Create a Connector

Configuring a connector in you public-debian feed will allow Debian packages from an OSS repository to be proxied to it. From your public-debian feed, select "Manage Feed" from the drop down menu.

![](){height="" width="50%"}

## Step 3: Adding the Feed to Your Local Debian Environment { #add-feed }

To add your `public-debian` feed to your local Debian environment, you will require the feed URL which can be found on the feed's page:

![](/resources/docs/proget-cran-feed.png){height="" width="50%"}

Then, run the following command to configure the feed as a source:

```
CLI goes here
```

In the case that the OSS repository you are proxying from is already configured in your Debian environment, you can remove it by entering:

```dotnetcli
CLI goes here
```

This will make sure any packages installed will be sourced from your `public-debian` feed.


## Step 4: (Optional) Confirming Connection to your Debian Feed

You can confirm that your local R environment is configured with your CRAN feed by entering:

```
CLI goes here
```

This will list the repositories currently configured, which should include your `public-debian` feed.

## (Optional) Authenticating to Your Debian Feed

By default your `public-debian` feed does require authentication and can be viewed anonymously. However, you may want to make your repository private and [configure it to require authentication to access](/docs/proget/feeds/debian#authentication). This is recommended when using internal packages in a feed, either on their own or in addition to using packages proxied from OSS repositories. 

## (Optional) Creating a Package Approval Flow

In this article, we explored how to proxy packages from OSS Debian repositories. However, configuring your Feed this way would let developers to use any OSS package from the a public repository without oversight. To avoid risks associated with potentially vulnerable or poorly maintained packages, you should include some form of oversight of packages used in production, which can be done by introducing a ["Package Approval Flow"](/docs/proget/packages/package-promotion).

To set up a package approval flow, refer to [HOWTO: Approve and Promote Open-source Packages](/docs/proget/packages/package-promotion/proget-howto-promote-packages). This guide uses NuGet feeds as an example, but the steps are identical when creating CRAN feeds.

After creating your "Unapproved" and "Approved" feeds, follow the steps in ["Adding the Feed to Your Local Debian Environment"](#add-feed) to add the "Approved" feed as a source in your local R environments, entering:

```
CLI goes here
```