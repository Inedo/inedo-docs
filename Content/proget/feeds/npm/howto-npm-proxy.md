---
title: "HOWTO: Proxy Packages from the npm Registry in ProGet"
order: 1
---

With ProGet you can create ["Feeds"](/docs/proget/feeds/feed-overview) to proxy packages from the [npm Registry](https://www.npmjs.com/) and install them just as you would when installing them from the npm Registry directly. 

Using ProGet as a proxy will cache packages, allowing teams to access them even if the npm Registry is down. ProGet will also tell you which packages are being downloaded and used frequently

This guide will cover how to set up a feed to proxy packages. We'll also cover how to create a private repository for your internal packages as an alternative to the npm Registry's paid private repositories (npm Teams, npm Enterprise).

## Step 1: Create a New Feed { #step-1 }

First, we will create an npm feed that will proxy packages from the [npm Registry](https://www.npmjs.com/).

Start by selecting "Feeds" and "Create New Feed". Next, select "npm Packages", as we will be creating feeds to proxy and host npm packages.

![](){height="" width="50%"}

Now select "Connect to npmJS.org" which will allow us to proxy packages from the npm Registry.

![](){height="" width="50%"}

Then select "No, Create One Feed", as we will be creating a single feed to proxy npm packages. From here, name the feed (we will call it `public-npm` for this guide). Then click "Create Feed".

![](){height="" width="50%"}

We are then presented with several options. More information on these can be found in the [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) documentation. Select "Set Feed Features", which will create the feed, and redirect you to the newly created `public-conda` feed, now populated with packages proxied from the npm Registry.

![](){height="" width="50%"}

## Step 3: Add the Feed to Local npm Environments { #step-3 }

For your team to install packages from the `public-npm` feed, you'll need to add it as a source in their local environment. For this, you will need feed's URL. This can be found at the top right of the feed's page.

![](){height="" width="50%"}





You can confirm that the public-npm feed has been configured correctly by entering:

```bash
$ conda search -c «feed-url»
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





