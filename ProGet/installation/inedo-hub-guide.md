---
title: ProGet 5.1 Installer
sequence: 500
keywords: proget, installation

---
# ProGet 5.1 Installation Guide {#51-install data-title="ProGet 5.1 Installation Guide"}

This is a step-by-step guide to installing ProGet on Windows and provides some detail as to what's happening behind the scenes.

# 1. Pre-Installation Check List {#pre-install data-title="1. Pre-Installation Check List"}

ProGet doesn't have any intense server requirements, and supports all modern version of Windows that Microsoft supports (see [Windows Lifecycle Fact Sheet](https://support.microsoft.com/en-us/help/13853/windows-lifecycle-fact-sheet). The recommended requirements are:

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

## 2. Download ProGet {#download data-title="2. Download ProGet"}

Starting with ProGet v5.1, ProGet uses the Inedo Hub for installation, uninstallation, and upgrades.

Download the ProGet 5.1 Beta installer from [download](/proget/download) on inedo.com.

## 3. Verify {#verify data-title="3. Verify"}

We always sign our installer packages. This signature can be seen when you run the installer and get a security warning, or right-click on the file for Properties and then look at Digital Signatures.

We've never had any issues with compromised installers (it's hosted on Amazon S3, which is quite reliable), but it doesn't hurt to make sure the signature is there.

## 4. Run the Installer {#run-install data-title="4. Run the Installer"}

The installer requires administrative privileges to run and will attempt to escalate. It will then unpack the temporary files and begin installation verification.

The downloaded installer will then download the latest version of Inedo Hub from Inedo's bucket on Amazon S3 and attempt to install it.

Once complete, the installer will exit and launch the Inedo Hub app, which you will use to install ProGet. Note that you do not need to download the hub installer directly again, you may simply launch the installed Inedo Hub app.

## 5. Click Install Button {#click-install data-title="5. Click Install Button"}

When launched, the Inedo Hub will show the latest available ProGet version with an Install button. Click the button.

## 6. Wait for Prerequisite Scanning {#prerequisite data-title="6. Wait for Prerequisite Scanning"}

At this point, the installer will attempt to detect whether IIS is installed on the system as well as any install SQL Server instances. Depending on the OS version and the machine, this can take anywhere from a few seconds to a few minutes.

## 7. Configure Install {#configure-install data-title="7. Configure Install"}

You are now prompted for information that the installer will need to begin installing
ProGet. This consists of:

{.docs}
- **Registration** - Select whether to register as a Free-edition user, an Enterprise Trial-edition user, or to skip automatic product registration. Note that if you choose not to register at this time, you will have to do so within the product itself after it has been installed if you want to be able to do anything useful.
- **SQL Server** - Select whether to install a SQL Server Express instance named "Inedo" or to supply a connection string to a SQL instance that you have already installed. If you opt to install the Inedo instance and you already have a database instance named "Inedo," it will not be installed again.
- **Database Name** - Choose the name of the SQL database that will be created (if it does not already exist) or used (if it already exists). This will override the database specified in any connection string. We recommend leaving it at the default unless you have a specific reason/policy requiring that it be different.
- **Hosting** - Since ProGet is a web application, it needs to be hosted somehow. If you select Integrated Web Server, a lightweight service is installed to provide this hosting. If you select IIS, an IIS AppPool and Web Site are created.

## 8. Begin Installation {#begin-install data-title="Begin Installation"}

When you are satisified with the configuration, click the Install button. If there are any issues with the database connection specified, you will be notified about it before installation begins. If you elected to register the product during installation, you will be prompted for your name and e-mail address before installation begins.

## 9. Patiently Wait {#patience data-title="Patiently Wait"}

If you chose to have SQL Server Express installed, then the actual installation process will take a while, as the installer must download the SQL installer and then wait for it to complete. On some servers, we've seen this take as long as fifteen minutes.

Clicking cancel (especially during the SQL Express installation) is not advised, as you may have to manually remove some bits of SQL Server using Add/Remove programs. Once everything's done, you'll be ready to start using ProGet.
