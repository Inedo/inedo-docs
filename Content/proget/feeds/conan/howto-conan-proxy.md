---
title: "HOWTO: Proxy Recipes from Conan Center in ProGet"
order: 1
---

ProGet lets you create ["Feeds"](/docs/proget/feeds/feed-overview) to proxy [Conan](https://conan.io) packages from [Conan Center](https://center.conan.io). This feed can then be configured as a remote locally, allowing you to install Conan packages as you would when installing them directly from Conan Center.

Using ProGet as a proxy will let you [assess vulnerabilities](#scan-feed) in Conan packages, provide statistics on which packages are being downloaded and used frequently, and also cache packages to ProGet, allowing your team to access and install them even if Conan Center is experiencing network issues.

This page will explain how to set up a Conan feed in ProGet to proxy packages. It will also cover how to create a private repository for your internal Conan packages.

## Step 1: Create a New Feed { #create-feed }

First, you will need to create a Conan feed that will proxy Conan packages from [Conan Center](https://center.conan.io). Select "Feeds" and "Create New Feed". Next, select "Conan Packages" under "Developer Libraries".

![](){height="" width="50%"}

Now select "Connect to conan.io" which will allow us to proxy packages from Conan Center.

![](){height="" width="50%"}

Then select "No, Create One Feed", as we will be creating a single feed to proxy Conan packages. From here, name the feed (in this example, we've called it `public-conan`). Then click "Create Feed".

![](){height="" width="50%"}

You'll then be given options to configure ProGet's [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) and [Licensing Detection and Blocking](https://docs.inedo.com/docs/proget/sca/licenses) features, letting you assess vulnerabilities and create policies for licenses. Select "Set Feed Features", which will create the feed, and redirect you to the newly created `public-conan` feed.

![](){height="" width="50%"}

:::(Info)(Conan Feeds & Connectors)
ProGet feeds configured with a connector will normally list packages proxied from an external source. However, Conan connectors do not support listing proxied recipes. Once you configure your Conan client to proxy all requests through this ProGet feed, you will start seeing cached recipes and packages.
:::

## Step 2: Add the Feed as a Remote to Your Conan Client { #add-feed }

To let your teams use the `public-conan` feed when installing packages, you will need to add it as a remote using the conan `remote add` command. 


## (Optional) Authenticating to Your PyPI Feed

By default your `public-pypi` feed does not need to be authenticated to can be viewed anonymously. However if you've configured your feed to require authentication, you can [authenticate to it](/docs/proget/feeds/pypi#authenticating-to-a-pypi-feed) when using `pip install` or with `pip config`. Alternatively you can authenticate with [PipEnv or Poetry](/docs/proget/feeds/pypi/integrate-pypi-others#authenticate-feed).

::: (Info) (💡 Best Practices: Use API Keys for Authenticated Feeds)
Rather than using your ProGet username/password for a NuGet feed, we strongly recommend [Creating a ProGet API Key](/docs/proget/reference-api/proget-apikeys) to authenticate, using `api` as the username and your key as the password.
:::

## (Optional) Creating a Package Approval Flow

In this guide we looked at proxying packages from PyPI. However, without oversight approval, developers will be able to install any OSS packages from [PyPI](https://pypi.org/) without oversight. We recommend implementing some form package vetting of your PyPI packages, which can be done by creating a ["Package Approval Flow"](/docs/proget/packages/package-promotion).

To set up a package approval flow, refer to [HOWTO: Approve and Promote Open-source Packages](/docs/proget/packages/package-promotion/proget-howto-promote-packages). The guide uses NuGet feeds as an example, but the steps are identical when [creating PyPI package feeds](#create-feed).

After creating your "Unapproved" and "Approved" feeds, follow the steps in ["Using the Feed in Python Clients"](#add-feed) to add the "Approved" feed (e.g. `pypi-approved`) as a source.