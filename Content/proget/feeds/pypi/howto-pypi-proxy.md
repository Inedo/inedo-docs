---
title: "HOWTO: Proxy Packages from PyPI.org in ProGet"
order: 1
---

ProGet let's you create ["Feeds"](/docs/proget/feeds/feed-overview) to proxy packages from The Python Package Index ([PyPI](https://pypi.org/)) and install them just as you would when installing them from PyPI directly. 

Using ProGet as a proxy will let you [assess vulnerabilities](#scan-feed) in PyPI packages, tell you which packages are being downloaded and used frequently, and cache packages, allowing teams to access them even if PyPI is down.

This article will look at how to set up a feed in ProGet to proxy PyPI packages, as well as how to create a private repository for your internal Python packages.

## Step 1: Create a New Feed { #create-feed }

To begin, we will create a PyPI feed that will proxy Python packages from [PyPI](https://pypi.org/).

Start by selecting "Feeds" and "Create New Feed". Next, select "Python Packages" under "Developer Libraries".

![](/resources/docs/proget-pypi-createfeed.png){height="" width="50%"}

Now select "Connect to PyPI.org" which will allow us to proxy packages from PyPI.

![](/resources/docs/proget-pypi-connectors.png){height="" width="50%"}

Then select "No, Create One Feed", as we will be creating a single feed to proxy PyPI packages. From here, name the feed (we will call it `public-pypi` for this example). Then click "Create Feed".

![](/resources/docs/proget-pypi-public-namefeed.png){height="" width="50%"}

We are then presented with several options. Keeping these checked will allow your feed to use ProGet's [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) amd [Licensing Detection and Blocking](https://docs.inedo.com/docs/proget/sca/licenses) features, allowing you to assess vulnerabilities and create policies for licenses. Select "Set Feed Features", which will create the feed, and redirect you to the newly created `public-pypi` feed, now populated with packages proxied from PyPI.

![](/resources/docs/proget-pypi-public-feed.png){height="" width="50%"}

## Step 3: Using the Feed in Python Clients { #add-feed }

To let your teams use the `public-pypi` feed when installing packages you can either include it when running the `pip install` command, or set it globally with the `pip config` command. 

To install Python packages with the `pip install` command, you will need to add a `--extra-index-url` parameter containing the endpoint URL of your `public-pypi` feed:

```bash
$ pip install «package-name»==«package-version» --extra-index-url https://«proget-server»/pypi/public-pypi/simple
```

To configure pip to use a [pip config](https://pip.pypa.io/en/stable/topics/configuration/) file to store the feed, you will need to use the [pip config](https://pip.pypa.io/en/stable/cli/pip_config/) command with a `--global` parameter containing your `public-pypi` endpoint URL.

```bash
$ pip config --global set global.index-url https://«proget-server»/pypi/public-pypi/simple
```

These commands will generate a `pip config` file that looks like:

```bash
[global]
index-url = https://«proget-server»/pypi/pypi/public-pypi/simple
```

::: (Info) (Note: Scoping)
The `pip config` can be scoped to global (`--global`), user (`--user`), and to the environment (`--site`). The commands above are scoped to the global scope.
:::

## (Optional) Authenticating to Your PyPI Feed

By default your `public-npm` feed does not require authentication and can be viewed anonymously. However if you've configured your feed to require authentication, you can use the two `pip` methods above to authenticate to it. [Feeds can be authenticated](/docs/proget/feeds/pypi#authenticating-to-a-pypi-feed) with a username and password string `«username»:«password»` but we strongly recommend using an [API Key](/docs/proget/reference-api/proget-apikeys) for this, with `api` as the username, and then API Key as the password.

## (Optional) Creating a Package Approval Flow

In this guide we looked at proxying packages from PyPI. However, without oversight approval, developers will be able to install any OSS packages from [PyPI](https://pypi.org/) without oversight. It's recommended to include some form package vetting in development or production, which can be done by creating a ["Package Approval Flow"](/docs/proget/packages/package-promotion).

To set up a package approval flow, refer to [HOWTO: Approve and Promote Open-source Packages](/docs/proget/packages/package-promotion/proget-howto-promote-packages). This guide uses NuGet feeds as an example, but the steps are identical when creating PyPI package feeds.

After creating your "Unapproved" and "Approved" feeds, follow the steps in ["Using the Feed in Python Clients"](#add-feed) to add the "Approved" feed (e.g. `npm-approved`) as a source using either `pip install` or `pip config`.