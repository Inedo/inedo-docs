---
title: "HOWTO: Proxy Packages from the Comprehensive R Archive Network (CRAN)"
order: 1
---

ProGet can be configured to proxy CRAN packages from the [Comprehensive R Archive Network (CRAN)](https://cran.r-project.org/web/packages/available_packages_by_name.html) so that teams can install them in their projects just as they would when selecting a CRAN mirror in their R CLI or an IDE like [RStudio](https://posit.co/downloads/). 

This is useful when the particular mirror you are using is experiencing issues, or when you want to easily see which packages are being downloaded and used frequently.

In this article, we'll explain how to create a ["Feed"](/docs/proget/feeds/feed-overview) in ProGet that will proxy packages from the CRAN repository. 

We’ll also discuss how to set up a private repository for using internal R packages, as well as how to establish a package approval process to manage which packages your team can utilize in production.

## Step 1: Create and Name a CRAN Feed

We'll start by creating a CRAN feed that will proxy packages from the [CRAN](https://cran.r-project.org/web/packages/available_packages_by_name.html) repository. You can read more on creating feeds by reading [Creating and Managing Feeds](/docs/proget/feeds/feed-overview#creating-and-managing-feeds)

In your ProGet instance, select "Feeds" and "Create New Feed", and select "CRAN Packages".

![](/resources/docs/proget-cran-newfeed.png){height="" width="50%"}

Then select "Connect to Official CRAN Repository" which will allow us to proxy and cache packages from the [CRAN](https://cran.r-project.org/web/packages/available_packages_by_name.html) repository.

![](/resources/docs/proget-cran-connector.png){height="" width="50%"}

Select "No, Create One Feed", as we will only need a single feed to proxy CRAN packages. You'll then need to name the feed. For this example, we will name it `public-cran`, and then click "Create Feed".

![](/resources/docs/proget-cran-publicfeed.png){height="" width="50%"}

We are then presented with several options. More information on these can be found in the [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) documentation. Select "Set Feed Features". ProGet will create The `public-cran` feed and redirect you to it. This feed will now be populated with packages proxied from the CRAN public repository.

![](/resources/docs/proget-cran-feed.png){height="" width="50%"}

## Step 2: Adding the Feed to Your Local R Environments { #add-feed }

To install packages from the `public-cran` feed, you will first need the URL of the `public-cran` feed. This can be found on the top right of the feed page:

![](/resources/docs/proget-cran-url.png){height="" width="50%"}

Then, you can simply enter the following command to install a package:

```r
install.packages("«package-name»", repos="«feed-url»")
```

For example, to install the package `devtools` from `http://proget.corp.local/cran/public-cran/` you would enter:

```r
install.packages("devtools", repos="http://proget.corp.local/cran/public-cran/")
```

However, we recommend setting your ProGet instance as a custom repository. This will configure R to look first at the specified URL for packages, instead of the default CRAN repository. You can do this by entering:

```r
options(repos = c(«repository-name» = "«feed-url»"))
```

For example, to create a custom repository with the name ProGet that points to `http://proget.corp.local/cran/public-cran/`, you would enter: 

```r
options(repos = c(ProGet = "http://proget.corp.local/cran/public-cran/"))
```

In the above example, we name the custom repository `ProGet`, though you can choose any name you prefer.

You can also configure this in RStudio by navigating to "Tools" > "Global Options" > "Packages" and selecting "Change".

![](/resources/docs/rstudio-addproget.png){height="" width="50%"}

And then entering your Feed URL in the "Custom" field.

![](/resources/docs/rstudio-customrepo-public.png){height="" width="50%"}

After setting the custom repository URL, you can simply install packages such as `devtools` by entering:

```r
install.packages("devtools")
```

Or in the RStudio interface, you can also navigate to "Tools" > "Install Packages..." and enter devtools in the "Packages" field.

![](/resources/docs/rstudio-install-devtools.png){height="" width="50%"}

## Step 3: (Optional) Confirming Connection to your CRAN Feed

You can confirm that your local R environment is configured with your CRAN feed by entering:

```r
getOption("repos")
```

This should list all repositories connected. If your ProGet instance is at the top, this indicates that it is set as the primary repository source for packages.

## (Optional) Authenticating to Your CRAN Feed

By default your `public-cran` feed will not require authentication and can be viewed anonymously. However, you may want to make your repository private and [configure it to require authentication to access](/docs/proget/feeds/cran#authenticating-to-cran-feeds). One reason for doing this would be when using internal packages in a feed, either solely or in addition to using OSS packages. 

## (Optional) Creating a Package Approval Flow

In this article, we explored how to proxy packages from the [CRAN](https://cran.r-project.org/web/packages/available_packages_by_name.html) repository. However, this allows developers to use any OSS package from the public repository without oversight. In many cases, it's important to include some form of approval or oversight in development or production, which can be done by introducing a ["Package Approval Flow"](/docs/proget/packages/package-promotion).

To set up a package approval flow, refer to [HOWTO: Approve and Promote Open-source Packages](/docs/proget/packages/package-promotion/proget-howto-promote-packages). This guide uses NuGet feeds as an example, but the steps are identical when creating CRAN feeds.

After creating your "Unapproved" and "Approved" feeds, follow the steps in ["Adding the Feed to Your Local R Environments"](#add-feed) to add the "Approved" feed as a custom repository in your local R environments, entering:

```r
options(repos = c(«repository-name» = "«feed-url»"))
```

Or, in RStudio by navigating to "Tools" > "Global Options" > "Packages", selecting "Change" and then entering your Feed URL in the "Custom" field.

![](/resources/docs/rstudio-customrepo-approved.png){height="" width="50%"}
