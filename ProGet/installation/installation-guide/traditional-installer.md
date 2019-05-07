---
title: Traditional Installer Guide
sequence: 500
keywords: proget, installation
---

::: {.attention .best-practice} 
For new installations on Windows, we recommend installation using the Inedo Hub. See the [Inedo Hub Installation Guide](/support/documentation/proget/installation/installation-guide) for more information.
:::

This is a step-by-step guide to installing ProGet on Windows and provides some detail as to what's happening behind the scenes.

## 1. Pre-Installation Check List {#pre-install data-title="Pre-Installation Check List"}

ProGet doesn't have any intense server requirements, and supports all modern version of Windows that Microsoft supports (see [Windows lifecycle fact sheet](https://support.microsoft.com/en-us/help/13853/windows-lifecycle-fact-sheet). The recommended requirements are:

{.docs}
- **Minimum** – 2 Core CPU, 2 GB RAM, 1 GB for storage
- **Recommended Average/Typical** – 2 Core CPU, 4 GB RAM, 10 GB storage
- **Recommended Large** –  4 Core CPU, 8 GB RAM, 16 GB Storage

Before installing, consider the following:

{.docs}
- **.NET Framework 4.5.2** – ProGet requires .NET 4.5.2, and Windows will prompt about this if you don't already have it
- **Web Server** – ProGet has its own web server, but can also be hosted in Microsoft IIS; if you want to use IIS, make sure it's installed first
- **Database Server** – ProGet does require Microsoft SQL Server, but the ProGet installer can install SQL Express for you
- **Disk Space** – ProGet does not use a whole lot, but your packages might
- **Firewall (Inbound)** – ProGet is a web application and can be installed on the port of your choosing
- **Firewall (Outbound)** – Connectors within ProGet connect to external NuGet feeds, and therefore require outbound access

## 2. Download ProGet {#download data-title="Download ProGet"}

To download the Traditional Installer, visit the [ProGet All Versions](/proget/versions) page, and download using the **Traditional Installer** link for the desired version.

## 3. Verify the Installer {#verify data-title="3. Verify the Installer"}

We always sign our installer packages. This signature can be seen when you run the installer and get a security warning, or right-click on the file for Properties and then look at Digital Signatures.

![](http://inedo.com/resources/knowledgebase/ProGet-Installation-Guide_E6DE_image_2.png){.screenshot}

We've never had any issues with compromised installers (it's hosted on Amazon S3, which is quite reliable), but it doesn't hurt to make sure the signature is there.

## 4. Run the Installer {#run-install data-title="4. Run the Installer"}

The installer requires administrative privileges to run and will attempt to escalate. It will then unpack the temporary files and begin installation verification.

## 5. License Agreement {#agreement data-title="5. License Agreement"}

Take a quick read of our license agreement. Really, it's quick.

![](/resources/documentation/proget-installation/Progetinstaller.png){.screenshot}

## 6. Edition / License Keys {#edition-license data-title="6. Edition / License Keys"}

After accepting the license agreement, select the edition of ProGet you'd like to use (Free or Enterprise Trial).

![](/resources/documentation/proget-installation/progetedition.png){.screenshot}

## 7. License Registration {#license-registration data-title="7. License Registration"}

After selecting the edition, just enter your name and email. The installer will try to get this from the currently logged in user.

![](/resources/documentation/proget-installation/progetlicense.png){.screenshot}

## 8. Target Directory {#target data-title="Target Directory"}

Deploy to your favorite location, or just stick with the default of Program Files.

![](/resources/documentation/proget-installation/progettargetdirectory.png){.screenshot}

## 9. Database Configuration {#configuration data-title="Database Configuration"}

If you ran the installer with the embedded SQL Express, then you'll be able to select the option to install SQL Express:

![](/resources/documentation/proget-installation/progetdatabase.png){.screenshot}

Otherwise, you'll need to point to an existing server using a standard SQL Connection string. Note that if no database is specified in the connection string (which is the default), a database named ProGet will be created.

## 10. Web Server Configuration {#web-config data-title= "10. Web Server Configuration"}

By default, the installer will configure ProGet to use its own web server. This is the easiest to start with, and you can always switch to IIS hosting later.

![](/resources/documentation/proget-installation/progetwebserver.png){.screenshot}

If IIS is installed on the server, you will have the option of installing ProGet in IIS. This will create an application pool and website in the local IIS configuration.

## 11. User Account {#user-account data-title="11. User Account"}

By default, the installer will use the Network Service account to run the ProGet Service and Web Application. We Recommend sticking with this, and changing the account later if you need to.

![](/resources/documentation/proget-installation/useraccount.png){.screenshot}

## 12. Summary {#summary data-title="12. Summary"}

Make sure everything here looks OK, and then click Install.

![](/resources/documentation/proget-installation/summary.png){.screenshot}

If any of the items indicate an error, you can click on it to go back to that page and fix it.

## 13. Patiently Wait {#patiently-wait data-title="Patiently Wait"}

If you chose to have SQL Server Express installed, then the actual installation process will take a while, as the installer must download the SQL installer and then wait for it to complete. On some servers, we've seen this take as long as fifteen minutes.

![](/resources/documentation/proget-installation/progetclickinstall.png){.screenshot}

Clicking cancel (especially during the SQL Express installation) is not advised, as you may have to manually remove some bits of SQL Server using Add/Remove programs. Once everything's done, you'll be ready to start using ProGet.
