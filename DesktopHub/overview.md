---
title: Inedo Hub Overview
sequence: 10
keywords: proget, installation
show-headings-in-nav: true
---

The Inedo Hub is primarily used to install, update, and otherwise service applications in the Inedo Suite.

## Installing the Hub {#installing data-title="Installing the Hub"}

The Inedo Hub is itself a lightweight installable Windows app. Currently, it can be downloaded from the [ProGet download page](https://inedo.com/proget/download) as a small executable file that downloads and installs the latest version of the hub when you execute it.

<iframe width="600" height="337" src="https://www.youtube.com/embed/yv7C-yrz1_E" frameborder="0" allowfullscreen="true"></iframe>


## Installation {#installation data-title="Installation"}

All products require an instance of SQL Server (any version of SQL Server currently supported by Microsoft may be used). The Hub can download and install a SQL Express instance – if this option is selected, an “Inedo” instance of SQL Express is installed (if the Inedo instance is already present on the local machine, that will be used and SQL Express will not be downloaded and installed again). If a connection string is supplied, the Hub must have permission to create and modify databases.

A web server is also required for all Inedo applications: the Hub can install products to use either an integrated web server or IIS. The integrated web server is easier to configure initially but lacks features that may be necessary in a production environment such as SSL support.

If IIS is selected in the Hub but is not currently installed on the server, you will be prompted to install it. When installing using IIS hosting, the Hub creates an AppPool named [ProductName]AppPool and a Web Application called [ProductName].

All products also require a license key from Inedo. The hub can request a free or trial key for you if you supply a name and email address, but you may also choose not to register at this time. If you install a product without registering, you will have to add the key within the software itself.

## Upgrades {#upgrades data-title="Upgrades"}

You can upgrade products to any greater version number by clicking on the Upgrade button and selecting the desired version. By default, the latest stable version is selected, but you can choose a lower version or a later [prerelease version](#prerelease) as well.

Upgrades also required a connection string. By default, the connection string used by the application is used, but you may supply an alternative one to use for the upgrade instead (this is necessary if the software runs as a lower-privilege user not able to make schema changes and you are not using integrated authentication).

## Uninstallation {#uninstallation data-title="Uninstallation"}

Individual products can be uninstalled by clicking on its associated Uninstall button. The uninstall operation stops and removes all services, IIS sites and app pools, and removes installed program files. Uninstallation does **not** remove any databases or data files such as packages, artifacts, or extensions.

## Service Inspection {#service data-title="Service Inspection"}

The Services tab displays the status of all Windows services for each installed product.

## Product Configuration {#product data-title="Product Configuration"}

The Configuration tab displays entries stored in each product's configuration file. The configuration file stores a small number of key/value pairs that are unsuitable for storage in the associated database, such as: connection string, integrated web server configuration, and encryption key.

## Uninstalling the Hub {#uninstalling data-title="Uninstalling the Hub"}

The Inedo Hub has no background services and only occupies a few megabytes of disk space - it is simply an app designed to make managing your Inedo product installations easier, so we recommend keeping it installed unless you are totally removing every product on a server and will no longer need it.

That said, The hub itself may be uninstalled by either using the Programs and Features dialog in Windows or executing the InedoHubUninstaller.exe program directly.

Uninstalling the hub will **not** uninstall all of the individual Inedo products automatically. If you want to uninstall everything, use the hub to uninstall each product first.

## Prerelease Product Versions {#prerelease data-title="Prerelease Product Versions"}

::: {.attention .best-practice}
<img src="/resources/images/icons/best-practices.png" alt="Check" />

Note that prerelease versions should be considered "beta", and should not be used in a production environment.
:::

The Inedo Hub may be configured to install prerelease product versions of any Inedo product, including CI and public beta versions. However, these versions are hidden by default. 

To configure the Inedo Hub to access prerelease versions, click on the `[config]` link at the bottom-left corner of the Inedo Hub and set the package source to:

```
https://proget.inedo.com/upack/PrereleaseProducts
```

Once this value is changed, restart the Inedo Hub, and prerelease versions will appear in the version dropdowns.
