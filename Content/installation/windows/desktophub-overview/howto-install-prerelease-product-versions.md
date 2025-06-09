---
title: "HOWTO: Install Pre-release Product Versions"
order: 4
---

The [Inedo Hub](/docs/installation/windows/desktophub-overview) may be configured to install pre-release product versions of any Inedo product, including CI and public beta versions. However, these versions are hidden by default. 

This article will walk you through how to install pre-release product versions of Inedo products.

:::(Info) (Use Pre-release Versions With Care)
Unless otherwise directed by our support engineers, pre-release versions should be considered "beta" and should not be used in a production environment.
:::

## Step 1: Configure The Inedo Hub to List Pre-release Versions
To configure the Inedo Hub to access pre-release versions, click on the *[config]* link in the bottom-left corner of the Inedo Hub. 

![Config Button](/resources/docs/prerelease-config.png)

Next, select "Inedo's Prerelease Feed"

![Package Source](/resources/docs/prerelease-packagesource-new.png)

## Step 2: Upgrade to The Relevant Pre-release Version
After saving the new package source in Step 1 you will have access to pre-release versions. Next to your Inedo product click either "Upgrade" or "Downgrade" and you will now see the pre-release versions as selectable options. 

![Pre-release versions](/resources/docs/prerelease-prereleaseversions.png)

Click on "Upgrade" under the Connection String and let the Inedo Hub do its magic!

## Step 3: Launch your Pre-release Inedo Product
After clicking "Upgrade" at the end of step 2 the Inedo Hub will take care of the rest. When the Inedo product is finished upgrading you will be brought back to the main Inedo Hub where your Inedo product will be displayed with the new version number.

![Pre-release ProGet](/resources/docs/prerelease-proget.png)

Click "Launch" to begin using the new version of your Inedo Product!

## Reverting The Inedo Hub Configuration
If you would like to revert the Inedo Hub back to the default setting that does not allow updating to pre-release versions, set the Package source to:

```
https://proget.inedo.com/upack/Products
```

