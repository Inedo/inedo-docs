---
title: "HOWTO: Install on Windows"
order: 1
---

The easiest way to install ProGet, BuildMaster, or Otter on Windows is with the [Inedo Hub](/docs/installation/windows/inedo-hub), a lightweight installer that acts as a one-stop shop for downloading, installing, upgrading/downgrading, and maintaining your Inedo products.

:::(Info)
**Looking to install on Linux?** Check out our [Docker installation guide](/docs/installation/linux/docker-guide).
:::

This article provides step-by-step instructions on using the Inedo Hub to install an Inedo product.

## Step 1: Download the Inedo Hub
The latest version of the Inedo Hub can be downloaded from [MyInedo](https://my.inedo.com/downloads?_gl=1*1k5rd2l*_ga*ODAzNTg0OTI3LjE2Mjc5NzI4NDk.*_ga_FNLRKW6N8G*MTY1NDA1MjU4Mi4xNjcuMS4xNjU0MDU0Njg1LjU4&_ga=2.202625425.1897008191.1653874612-803584927.1627972849). You can also download the Inedo Hub from the download page of any Inedo product.

![Download Inedo Hub](/resources/docs/windowsinstall-downloadinedohub.png)

## Step 2: Install the Inedo Hub
Now that you have downloaded the Inedo Hub you will need to install it by running the `InedoHubInstaller.exe` file you downloaded. 

:::(Warning)
You may get a security warning message depending on your Windows UAC Settings. Simply click "Run" after verifying that the publisher of the file is INEDO, LLC.
:::

After the Inedo Hub installer downloads all necessary files to install itself, you will see the main screen of the Inedo Hub.

![Inedo Hub](/resources/docs/inedohub-install.png)

## Step 3: Install a Product with the Inedo Hub
Any version of an Inedo product can be installed from the main screen of the Inedo Hub (pictured above). For the purposes of this tutorial, we will install ProGet via the Inedo Hub, but the instructions are identical for any Inedo product. 

Select the version of the product you want to install by clicking on the version drop-down menu, followed by the "Install" button.


![Inedo Hub ProGet Install](/resources/docs/windowsinstall-inedohubproget.png)

Next, you'll be able to customize your installation. Note that the options are identical for all Inedo products and you can change this configuration after installation.

![ProGet Installation Options](/resources/docs/windowsinstall-progetinstalloptions.png)


| Setting | Description |
| --- | --- |
| SQL Server | Choose between installing Inedo's SQL Server Instance or using an already installed instance on your server. Note: if you select the option to install Inedo's SQL server while you already have Microsoft SQL installed, the installer will simply check for updates to your existing server and install them if needed. It will then proceed to add the INEDO instance to the server. |
| Database Name | The name of the database that will be installed for your Inedo Product.|
| Hosting | Choose between using Inedo's integrated Web Server or configuring your Inedo product for use with Microsoft IIS. |
| Target Directory | The install path for your Inedo product. The default path is *C:\Program Files\«Inedo-product»* but it can be changed to match your desired location. |

Once your Inedo product is set up to match your desired configuration, click "Install".

The Inedo Hub will now automatically download and install your desired Inedo product. Once the installation is complete, you'll be returned to the home screen where your product will now display as Installed.

![Inedo Hub After Installing ProGet](/resources/docs/inedohub-installation-installed.png){height="" width="50%"}

That's it! Follow these 3 easy steps to quickly install any Inedo product. You can also launch, uninstall, and upgrade or downgrade your installed Inedo product from the Inedo Hub.

## Upgrading from the Inedo Hub
The "Upgrade" button will appear if an update is available. See [HOWTO: Upgrade or Downgrade](/docs/installation/windows/howto-upgrade-downgrade) to learn more.

In offline Inedo Hub instances you won't be notified about updates so we recommend viewing our [Detailed Change Log](/docs/installation/upgrading#viewing-change-logs) and [Upgrade Guidance](/docs/installation/upgrading#viewing-upgrade-guidance) on MyInedo regularly to stay informed about new releases.