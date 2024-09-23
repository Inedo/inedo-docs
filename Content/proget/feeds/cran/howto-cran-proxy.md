---
title: "HOWTO: Proxy Packages from the Comprehensive R Archive Network (CRAN)"
order: 1
---

ProGet can be configured to proxy CRAN packages from the [Comprehensive R Archive Network](https://cran.r-project.org/web/packages/available_packages_by_name.html) so that teams can install them in their projects just as they would when selecting a CRAN mirror in their R CLI or IDE. 

This is useful when the particular mirror you are using is experiencing issues, or when you want to easily see which packages are being downloaded and used frequently.

In this article, we'll explain how to create a ["Feed"](/docs/proget/feeds/feed-overview) in ProGet that will proxy packages from the CRAN package repository. 

We’ll also discuss how to set up a private repository for using internal CRAN packages, as well as how to establish a package approval process to manage which packages your team can utilize in production.

## Step 1: Create a New Feed { #step-1 }

We'll start by creating a CRAN feed that will proxy packages from the [Comprehensive R Archive Network](https://cran.r-project.org/web/packages/available_packages_by_name.html).

In your ProGet instance, select "Feeds" and "Create New Feed".

![New Feed](){height="" width="50%"}

Next, select "CRAN Packages".

![Select Conda](){height="" width="50%"}

Then select "Connect to Official CRAN Repository" which will allow us to proxy and cache packages from the [Comprehensive R Archive Network](https://cran.r-project.org/web/packages/available_packages_by_name.html)

![Connector](){height="" width="50%"}

Now select "No, Create One Feed", as we will only need a single feed to proxy Conda packages.

![One Feeds](){height="" width="50%"}

## Step 2: Name Your Feed

Next we name our feed. For this example we will name it `public-cran`, and then click "Create Feed".

![Name Feed](){height="" width="50%"}

We are then presented with several options. More information on these can be found in the [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) documentation.

![SCA Features](){height="" width="50%"}

Then select [Set Feed Features]. ProGet will create The `public-cran` feed and redirect you to it. This feed will now be populated with packages proxied from the CRAN public repository.

![Feed](){height="" width="50%"}

## Step 3: Adding the Feed to Your Local R Environments { #step-3 }

To install packages from the `public-conda` feed, you will first need the URL of the `public-cran` feed. This can be found on the top right of the feed page:

![Feed](){height="" width="50%"}

Then, you can simply enter the following command to install a package:

```
install.packages("«package-name»", repos="«feed-url»")
```

For example, to install the package `devtools` from `http://proget.corp.local/cran/public-cran/` you would enter:

```r
install.packages("devtools", repos="http://proget.corp.local/cran/public-cran/")
```

However, we recommend setting your ProGet instance as a custom repository. This will configure R to look first at the specified URL for packages, instead of the default CRAN repository. You can do this by entering:

```
options(repos = c(«repository-name» = "«feed-url»"))
```

For example, to create a custom repository with the name ProGet that points to `http://proget.corp.local/cran/public-cran/`, you would enter: 

```r
options(repos = c(ProGet = "http://proget.corp.local/cran/public-cran/"))
```

In the above example we give the custom repository the name `ProGet`, however you can give it any name you want.

Now you can simply install packages such as `devtools` by entering:

```r
install.packages("devtools")
```

## Step 4: (Optional) Confirming Connection to your CRAN Feed

You can confirm that your local CRAN environment is configured with your CRAN feed by entering:

```r
getOption("repos")
```

This should list all repositories connected. If your ProGet instance is at the top, this indicates that it is set as the primary repository source for packages.

## (Optional) Authenticating to Your CRAN Feed

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