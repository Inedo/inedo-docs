---
title: "Offline Installation (no Internet access)"
order: 3
---

By default, when you install ProGet, BuildMaster, or Otter using [the Inedo Hub](/docs/installation/windows/desktophub-overview) internet access is required. This is because the Inedo Hub will connect to Inedo's servers to upgrade itself and show you what Inedo products and versions are available to install.

However, the Inedo hub can also operate in a completely offline/disconnected state. This is useful if you need to update an Inedo product on a server that does not have Internet access. You can also [set up your own Inedo products feed](#setting-up-your-own-inedo-products-feed) for use on your intranet.

When using Inedo products offline you won't receive notifications about new releases. We recommend visiting your MyInedo page regularly and viewing our [Change Logs](/docs.inedo.com/docs/myinedo/viewing-change-logs) and [Upgrade Guidance](/docs/installation/upgrading#viewing-upgrade-guidance), to make sure that your software is secure and optimized.

## Downloading the Offline Installer
Starting from BuildMaster v7, Otter v3, and ProGet v6, you can download an offline installer from the [downloads page at my.inedo.com](https://my.inedo.com/downloads). These installers contain a single version of a single Inedo product, and can be run with no internet access.

:::(Info)
Single-exe offline installers are *not* suitable for silent or automated installation, as they are simply self-extracting zip files that run the InedoHub.exe  See the [Silent Installation](/docs/inedo-agent/inedoagent-installation-installation-guide/inedoagent-installation-silent-installation) to learn how to automate installation.
:::

## Creating a Custom Offline Installer
To create an offline Inedo Hub, follow these steps:

1. Install Inedo Hub on a workstation/server that has access to the Internet.
2. Click the "Create Offline Installer" button on the main page of the Inedo Hub application.
3. Select the versions of Inedo products you'd like to include in the offline installer. By default, the latest stable version of each product is selected.
4. Select the destination directory where the offline installer will be created. This directory will be created if it does not exist. If it does exist, it must be empty.
5. Click the "Create Installer" button and wait for the packages to be downloaded.
6. Copy the files in the destination directory to the server where you'd like to install one of the selected products.
7. Run InedoHub.exe on the server, and install the desired app.

### Creating a Custom Single-exe Offline Installer
Instead of having a directory of files, or a `.zip`file that you need to extract, you may wish to have a single-file executable offline installer, similar to what we provide on the downloads page. 

There are many tools that can help create a single-exe installer, and the process generally involves creating a self-extracting archive (zip file) that runs an extracted executable after extraction.

### Example Using the LZMA SDK
This is how we create single-exe bundles, and it involves using files from [7-zip](https://www.7-zip.org/) (which you likely already have), and the `7zS.sfx`plugin from the [LZMA SDK](https://www.7-zip.org/sdk.html).

1. Copy `7zS.sfx`, `7za.dll`, `7za.exe`, and `7zxa.dll` to `C:\OfflineHub`
2. Create `C:\OfflineHub\config.txt` with the following contents:
 

 ```
 ;!@Install@!UTF-8!
Title="Inedo Hub Offline Installer"
RunProgram="InedoHub.exe"
;!@InstallEnd@!
```

3. Create an offline Inedo Hub following the instructions above to `C:\OfflineHub\src`
4. Run the following commands

```
cd c:\OfflineHub\src
..\7za.exe -r a ..\pack.7z *.*
cd..
copy /b 7zS.sfx + config.txt + pack.7z OfflineInstaller.exe
```

The file `OfflineInstaller.exe` will be a self-extracting executable that runs the `InedoHub.exe` upon extraction. 

Note that you *could* modify this process to run `hub.exe` instead (i.e. [Inedo Hub CLI](/docs/installation/windows/desktophub-overview/desktophub-inedo-hub-cli)) and then pass in arguments as you would in a silent installation. However, this is more complicated and we wouldn't recommend it. Once you're scripting an install, you can simply download a .zip file and extract it with two one or two commands.

## Setting up your own Inedo products feed
By default, the Inedo Hub will connect to [a Universal Package Feed](/docs/proget/feeds/feed-overview) our [public ProGet instance](https://proget.inedo.com/) to upgrade itself and show you what Inedo products and versions are available to install.

You can configure the Inedo Hub to access a different feed, such as our Pre-release or a feed hosted on your own ProGet server. To connect to your own ProGet server, you can simply configure a [Connector](/docs/proget/feeds/connector-overview) to our Products feed.

| Products Feed | Endpoint URL |
| -- | -- |
| Stable (default) | `https://proget.inedo.com/upack/Products`
| Pre-release | `https://proget.inedo.com/upack/PrereleaseProducts`
