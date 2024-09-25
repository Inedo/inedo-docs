---
title: "HOWTO: Create and Upload CRAN Packages to a Private Repository in ProGet"
order: 2
---

ProGet let's you set up private repositories for your internal R packages that will allow you to store, publish, and share them within your organization.

This guide will walk you through the process of setting up a CRAN ["Feed"](/docs/proget/feeds/feed-overview) in ProGet as a private, custom repository. We'll also cover how to create, upload, and install packages from this repository.

## Step 1: Create and Name a CRAN Feed

We'll begin by creating a CRAN feed to host your R packages. Navigate to "Feeds" and "Create New Feed". Then select "CRAN Packages".

![](/resources/docs/proget-cran-newfeed.png){height="" width="50%"}

Now select "No Connectors (Private packages only)" as this feed will be intended as a private repository.

![](/resources/docs/proget-cran-noconnector.png){height="" width="50%"}

From here, we name our feed. For this example, we will call it `internal-cran`, and then click "Create Feed".

![](/resources/docs/proget-cran-internalfeed.png){height="" width="50%"}

You'll then see several options related to ProGet's [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) features. These are only for users looking to use third-party OSS packages. Leave these boxes unchecked, and select [Set Feed Features].

![](){height="" width="50%"}

You will then be directed to the new `internal-cran` feed, currently empty.

![](){height="" width="50%"}

## Step 2: Create an API Key { #step-2 }

Next, we'll create an [API Key](/docs/proget/reference-api/proget-apikeys) allowing our local client to authenticate to our `internal-cran` feed. This allows us to upload and install packages from the feed.

Start by navigating to "Administration Overview" > "API Keys & Access Logs" under "Security & Authentication"

![](/resources/docs/proget-admin-apikeys.png){height="" width="50%"}

Then select "Create API Key"

![](/resources/docs/proget-apikey-new.png){height="" width="50%"}

Next, fill in the fields by selecting "Feeds (Use Certain Feeds)" as the "Feed Type" and selecting the `internal-cran` feed. Then set the API key. You can use any alphanumeric sequence, or just leave it blank to autogenerate one.

![](){height="" width="50%"}

Make sure the "View/Download" and "Add/Repackage" boxes are checked, and then select "Save".

## Step 3: Build Your R Package

Next, we will build our R packages. More information on developing these can be found in [the official documentation](https://cran.r-project.org/web/packages/rcompendium/vignettes/developing_a_package.html). The commands in this step can be performed in either your [R](https://cran.r-project.org/bin/windows/base/) CLI or the [RStudio](https://posit.co/downloads/) console. 

To build your package, you'll first need `devtools` installed. To do this enter:

```r
install.packages("devtools")
library(devtools)
```

Or in the RStudio interface, you can also navigate to "Tools" > "Install Packages..." and enter devtools in the "Packages" field.

![](/resources/docs/rstudio-install-devtools.png){height="" width="50%"}
 
If you're using a CLI you're also need to make sure that the current working directory is set to the folder that your package files are located in (e.g. `DESCRIPTION`, `NAMESPACE` and `.r` files) by entering `setwd("path/to/package")`

Before building, we also recommend generating documentation by entering `devtools::document()` and then running a check with `devtools::check()` to make sure your project is free from errors.
 
Then build your package by entering:

```r
devtools::build()
```

Or in RStudio, you can alternatively navigate to the "Build" tab and select "Build Source Package".

![](/resources/docs/rstudio-build.png){height="" width="50%"}

Your `.tar.gz` R package is then built, and saved to the same location as your project folder. 

### Step 4: Upload Your Package to ProGet

To upload your package to your `internal-cran` CRAN feed, you can use Inedo's [pgutil](/docs/proget/reference-api/proget-pgutil) tool.

pgutil will require some [minor configuration](/docs/proget/reference-api/proget-pgutil#sources) before use. This includes configuring your ProGet instance and API key as a source by running:

```bash
$ pgutil sources add --name=Default --url=«proget-url» --api-key=«api-key»
```

For example, adding the ProGet instance `https://proget.corp.local/` with the API Key `abc12345` you would enter:

```bash
$ pgutil sources add --name=Default --url=https://proget.corp.local/ --api-key=abc12345
```

Now upload your packages by entering:

```bash
$ pgutil packages upload --feed=«feed-name» --input-file=«path-to-package»
```

For example, to upload the package `my-package-0.1.0-0.tar.gz` stored at `C:\development\cran_packages\` to your `internal-cran` feed you would enter:

```bash
$ pgutil packages upload --feed=internal-cran --input-file==C:\development\cran_packages\my-package-0.1.0-0.tar.gz
```

Your package will then be uploaded to the `internal-cran` feed.

![](){height="" width="50%"}

## Step 5: Add the Feed to Local R Environments

To install R packages you have published to your `internal-cran` feed, you'll need to add the feed to your local R environments. For this, you will need the feed's URL. This is found at the top right of the feed's page.

![](){height="" width="50%"}

Then, any time you want to install a package, take the `internal-cran` feed URL and the API key you created in [Step 2](#step-2), and in enter:

```r
install.packages("«package-name»", repos="http://api:«api-key»@«feed-url»")
```

For example, to install the package `devtools` from `http://proget.corp.local/cran/public-cran/`, authenticating with `abc12345` you would enter:

```r
install.packages("devtools", repos="http://api:abc12345@proget.corp.local/cran/public-cran/")
```

However, to avoid having to type in the repo URL every time, you can set your ProGet instance as a custom repository. This will configure R to look first at the specified URL for packages, instead of the default CRAN repository. You can do this by entering:

```r
options(repos = c(«repository-name» = "http://api:«api-key»@«feed-url»"))
```

For example, to create a custom repository with the name ProGet that points to `http://proget.corp.local/cran/public-cran/` authenticating with abc12345, you would enter: 

```r
options(repos = c(ProGet = "http://api:abc12345@proget.corp.local/cran/public-cran/"))
```

In the above example, we name the custom repository `ProGet`, though you can choose any name you prefer.

Now you can simply install packages such as `devtools` by entering:

```r
install.packages("devtools")
```

Or, in RStudio, by navigating to "Tools" > "Install Packages...". Then entering the package name in the "Package" field, ensuring that your ProGet feed is selected in the "Install From" field.

![](){height="" width="50%"}

## Step 6: (Optional) Confirming Connection to your CRAN Feed

You can confirm that your local R environment is configured with your CRAN feed by entering:

```r
getOption("repos")
```

This should list all repositories connected. If your ProGet instance is at the top, this indicates that it is set as the primary repository source for packages.