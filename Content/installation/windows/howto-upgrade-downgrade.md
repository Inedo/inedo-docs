---
title: "HOWTO: Upgrade or Downgrade on Windows"
nav-title: "HOWTO: Upgrade or Downgrade"
order: 2
---

ProGet, BuildMaster, and Otter are self-managed, which means that you are in complete control of when, and how often to upgrade. This lets you eliminate the risk of being "surprised" by unwanted new features or undetected bugs.

This article provides step-by-step instructions for how to upgrade of downgrade an Inedo product with the [Inedo Hub](/docs/installation/windows/inedo-hub). 

## Step 1: Decide If You Should Upgrade Your Inedo Product
Before updating to a newer version, you should weigh the costs associated with the update process against the value and benefits of the new features or bug fixes added. 

We have an article to help you [decide when to upgrade](/docs/installation/upgrading) and see if upgrading is the right decision, and when to do it. 

For minor releases, you can see detailed changelogs at [MyInedo](https://my.inedo.com/downloads).

For major releases, we'll make a document detailing what is included in the update and some important things to keep in mind before updating. For example:
* [Upgrading ProGet 2022](/docs/proget-upgrade-2022)
* [Upgrading to Otter 2022](/docs/otter-upgrade-2022)
* [Upgrading to BuildMaster 7.0](/docs/buildmaster-upgrading-to-v7)

## Step 2: Decide Which Version to Upgrade To
When an upgrade is available the "Downgrade" button will be replaced with "Upgrade". Note that you can still click on "Upgrade" and select an older version to downgrade to. 

![Inedo Hub Main Screen](/resources/docs/updatinginedohub-mainscreen.png){height="" width="50%"}

## Step 3: Perform the Upgrade
:::(Info) (Backing Up Your Data)
Before upgrading your Inedo product, we recommend [performing a back-up](/docs/installation/backing-up-restoring), just in case something goes wrong and you need to rollback. This is especially important when performing a major upgrade.
:::


Click on "Upgrade" next to the Inedo product you wish to upgrade, select the desired version, and click on "Upgrade" below the Connection String. You can also [upgrade to a pre-release version](/docs/installation/windows/inedo-hub/howto-install-prerelease-product-versions) of an Inedo product! 

![Version Selection](/resources/docs/inedohub-upgrade-chooseversion.png){height="" width="50%"}

## Step 4: Launch your Upgraded Inedo Product

After clicking "Upgrade" at the end of step 2 the Inedo Hub will take care of the rest. When the Inedo product is finished upgrading you will be brought back to the main Inedo Hub where your Inedo product will be displayed with the new version number.

![Inedo Hub After Upgrading BuildMaster](/resources/docs/updatinginedohub-buildmasterdownloaded.png){height="" width="50%"}

Click "Launch" to begin using the new version of your Inedo Product!

## How to Downgrade
When you may need to downgrade simply follow the same steps above, but instead of selecting a new version in step 2 you will select the relevant older version you wish to downgrade too. 

