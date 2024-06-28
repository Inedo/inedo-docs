---
title: "CRAN (R)"
order: 17
---

The Comprehensive R Archive Network ([CRAN](https://cran.r-project.org/)) is the main repository for packages written for the R language. Since version 2023.23, ProGet supports CRAN feeds to host R packages.

### Installing Packages

R packages are installed using the `install.packages` method. To install a package from a ProGet feed, use the following command:

```r
install.packages("{package-name}", repos="http://{proget-server}/cran/{feed-name}/")
```

#### Installing Specific Versions
The `install.packages` method can only install the latest version of a package. If you need to install a previous version, the easiest way is to download the package first and install from the file.

You can also use the [`install_version` function](https://remotes.r-lib.org/reference/install_version.html) from the [remotes](https://remotes.r-lib.org/) library.

#### Authenticated Feeds

If you've configured your feed to require authentication, you must pass a user name and password in using the url:

```r
install.packages("{package-name}", repos="https://{user-name}:{password}@{proget-server}/cran/{feed-name}/")
```

### Creating Packages

To learn how to create an R package that can be hosted by ProGet, see [R Packages by Hadley Wickham and Jennifer Bryan](https://r-pkgs.org/).

### Publishing Packages

R has no built in command to publish a package, but you can push a package to a ProGet feed from R using the `httr` package:

```
httr::POST("{proget-server}/cran/{feed-name}/",
    httr::authenticate("{user-name}", "{password}"),
    body = httr::upload_file("{package-file.tar.gz}")
)
```

Currently, you can only publish source package to a CRAN feed in ProGet. Binary packages can still be proxies by connectors, but not uploaded directly. See Limitations below for more information.

### Connecting to External Repositories

You can create a connector to any CRAN-like repository such as `https://cran.r-project.org/`. This will allow you to proxy the external feed through ProGet, including any binary packages, but browsing through the web application is limited, since CRAN repositories typically only expose the latest version of each package with very little accompanying metadata.

### Technical Limitations


#### Binary Packages

ProGet can store binary packages, but currently the only way to add them to your feed is by caching them through a connector. Binary packages are typically built by CRAN rather than by package authors directly, so ProGet doesn't support directly uploading them. Let us know if you'd like this capability and we will consider adding it to a future version.
