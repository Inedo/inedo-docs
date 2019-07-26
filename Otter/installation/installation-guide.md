---
title: Otter Installation Guide
keywords: otter
sequence: 100
---

<style>
    .documentation-content-wrapper .upcoming {
        background-color: #fff;
        position: relative;
    }
    .screenshot {
        opacity: .4;
    }
    .documentation-content-wrapper .upcoming.buildmaster::before {
        content: ' -- Placeholder screenshot from BuildMaster -- ';
        background-color: #f15a24;
    }
    .documentation-content-wrapper .upcoming::before {
        z-index: 1;
        width: 100%;
        background-color: #3fa9f5;
        padding: 2px 4px;
        color: white;
        content: ' -- Placeholder screenshot from ProGet -- ';
        text-align: center;
    }
</style>

Getting Otter up-and-running takes just a few minutes: just [download](/otter/download) and run the installer. This guide will provide step-by-step instructions, as well provide some detail as to what's happening behind the scenes.

## 1. Pre-Installation Check List {#pre-install}

Otter doesn't have any intense server requirements, and supports all modern version of Windows that Microsoft supports (see [Windows lifecycle fact sheet](https://support.microsoft.com/en-us/help/13853/windows-lifecycle-fact-sheet)). The recommended requirements are:

{.docs}
- **Minimum** – 2 Core CPU, 2 GB RAM, 1 GB for storage
- **Recommended Average/Typical** – 2 Core CPU, 4 GB RAM, 10 GB storage
- **Recommended Large** – 4 Core CPU, 8 GB, 16 GB Storage

Before installing, consider the following:

{.docs}
- **.NET Framework 4.5.2** – Otter requires .NET 4.5.2, and Windows will prompt about this if you don't already have it
- **Web Server** – Otter has its own web server, but can also be hosted in Microsoft IIS as well; if you want to use IIS, make sure it *and* ASP.NET 4.5 is installed first (see [KB2736284](https://support.microsoft.com/en-us/kb/2736284) for instructions)
- **Database Server** – Otter does require Microsoft SQL Server, but the Otter installer can install SQL Express
- **Firewall (Inbound)** – Otter is a web application and can be installed on the port of your choosing

## 2. Download Otter {#download}

You can [download the installer](/otter/download) from inedo.com.

## 3. Verify the Installer {#verify}

We always sign our installer packages. This signature can be seen when you run the installer and get a security warning, or right-click on the file for Properties and then look at Digital Signatures.

![](/resources/knowledgebase/ProGet-Installation-Guide_E6DE_image_2.png)

We've never had any issues with compromised installers (it's hosted on Amazon S3, which is quite reliable), but it doesn't hurt to make sure the signature is there. See [KB#1113: Anti-malware False Positives, Code Signing, and Safety of Inedo Products](/support/kb/1113) for more information.

## 4. Run the Installer {#run}

The installer requires administrative privileges to run and will attempt to escalate. It will then unpack the temporary files and begin installation verification.

## 5. License Agreement {#license-agreement}

Take a quick read of our [Otter license agreement](/otter/license-agreement). Really, it's quick!

![Otter License agreement](/resources/documentation/otter-installation/license-agreement-otter.png)

## 6. Edition / License Key {#edition-key}

After accepting the license agreement, select the edition of Otter you'd like to use (Free, Enterprise Trial, or Enter Key).

![Otter Edition License Key](/resources/documentation/otter-installation/edition-license-key-otter.png)

## 7. License Registration {#registration}

After selecting the edition, just enter your name and email. The installer will try to get this from the currently logged in user. We promise to not fill your inbox with pictures of [Otters Who Look Like Benedict Cumberbatch](https://www.google.com/search?tbm=isch&q=benedict+cumberbatch+otter "it's exactly what you think"){target="_blank"}

![Otter License Registration](/resources/documentation/otter-installation/license-registration-otter.png)

## 8. Target Directory {#directory}

Deploy to your favorite location, or just stick with the default of Program Files.

![Otter Target Directory](/resources/documentation/otter-installation/target-directory-otter.png)

## 9. Database Configuration {#database-configuration}

If you ran the installer with the embedded SQL Express, then you'll be able to select the option to install SQL Express.

![Otter Database Configuration](/resources/documentation/otter-installation/database-configuration-otter.png)

Otherwise, you'll need to point to an existing server using a standard SQL Connection string. Note that if no database is specified in the connection string (which is the default), a database named ProGet will be created.

## 10. Web Server Configuration {#web-server-configuration}

By default, the installer will configure Otter to use its own web server.  This is the easiest to start with, and you can always [switch to IIS](/support/kb/1013) hosting later.

![Otter Web Server Configuration](/resources/documentation/otter-installation/webserver-configuration-otter.png)

If IIS is installed on the server, you will have the option of installing Otter in IIS. This will create an application pool and website in the local IIS configuration.

## 11. User Account {#user-account}

By default, the installer will use the [NetworkService](https://msdn.microsoft.com/en-us/library/windows/desktop/ms684272(v=vs.85).aspx) account to run the [Otter Service](/docs/otter/administration/service) and Web Application. We recommend sticking with this, and [changing the account](/support/kb/1077) later if you need to.

![Otter User Account](/resources/documentation/otter-installation/user-account-otter.png)

## 12. Summary {#summary}

Make sure everything here looks OK, and then click Install.

![Otter Installation Summary](/resources/documentation/otter-installation/summary-otter.png)

If any of the items indicate an error, you can click on it to go back to that page and fix it.

## 13. Patiently Wait

If you chose to have SQL Server Express installed, then the actual installation process will take a little while. On embarrassingly low-end servers (e.g. Intel Celeron processor and 256MB of RAM), we've seen this take as long as fifteen minutes.

![Otter Patiently Wait](/resources/documentation/otter-installation/patiently-wait-otter.png)

While you wait, you can always peruse [Otters Who Look Like Benedict Cumberbatch](https://www.google.com/search?tbm=isch&amp;q=benedict+cumberbatch+otter "it's exactly what you think"){target="_blank"}.

Clicking cancel (especially during the SQL Express installation) is not advised, as you may have to manually remove some bits of SQL Server using Add/Remove programs. Once everything's done, you'll be ready to start using Otter. {.announcement}

## 14. All Done

Click the absurdly large **Launch Otter** button, and your web browser will start up with <code>http://localhost:&laquo;port-you-chose&raquo;/</code>.

![](/resources/documentation/otter-installation/all-done-otter.png)

Note that on slower systems, the services take a little bit to spin up, so if you see a blank page, just refresh the page until it loads.

Once Otter loads, we suggest going through the [Getting Started with Otter](/support/tutorials/otter/getting-started-with-otter-and-iac) tutorial.
