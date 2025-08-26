---
title: "Getting Started with ProGet"
order: 1
---

ProGet is a Self-hosted, Cross-Platform Package & Container Repository that keeps all your packages and Docker containers in one place, allowing you to scan for vulnerabilities and control who can access different feeds. 

:::(Info) (📺 3-minute Video:  Getting Started with ProGet)
<iframe width="600" height="337" src="https://www.youtube.com/embed/IQ_GzpgLhlY" frameborder="0" allowfullscreen="true"></iframe>
:::

ProGet supports just about all of the third-party package types you'll need (e.g. NuGet, npm, PowerShell, and Chocolatey) and comes in a powerful free version that you can upgrade when ready.

## Step 1: Install ProGet

You can install ProGet [quickly and easily from InedoHub](/docs/installation/windows/howto-install).

![Install ProGet](/resources/docs/inedohub-install.png){height="" width="50%"}

Once successfully installed, there will be a "Launch" button - it just loads `http://localhost:8624`, which is the default port for ProGet.

You also have the option of installing it using [Docker](/docs/installation/linux/docker-guide) or onto a [Lightsail instance](/docs/proget/installation/proget-how-to-install-on-aws-lightsail), both of which are just as quick and easy. 



## Step 2: Create Your First Feed in ProGet

A feed is a private repository for your own applications and components. Feeds are used to store packages, container images, or other assets. Users can easily see what is available for download in a feed. 

In this example, we will create a NuGet feed, a package format developed by Microsoft to distribute free and open-source .NET libraries. However, you have the option of creating feeds for other feed types such as npm, python, etc.

We will start by navigating to "Feeds" and selecting "Create New Feed". 

![Create New Feed](/resources/docs/proget-feeds-createnewfeed.png){height="" width="50%"}

As we will be using packages from [Nuget.org](https://www.nuget.org), we select "NuGet (.NET) Packages". 

![Create NuGet Feed](/resources/docs/proget-feeds-nugetselect.png){height="" width="50%"}

## Step 3: Set Up Your Feed 

After selecting the feed type, we’ll specify that the feed will connect directly to "NuGet.org", the public repository for open-source NuGet packages. 

![NuGet Connector](/resources/docs/proget-nuget-connecttoorg.png){height="" width="50%"}

We then name our feed. In this case, we will call it `public-nuget` as it will have public packages from Nuget.org. You'll also want to make sure the "Create connector to NuGet.org" box remains checked to set up a [connector](/docs/proget/feeds/connector-overview). Once that's done, click "Create New Feed". 

![Name NuGet Feed](/resources/docs/proget-nuget-nameonefeed.png){height="" width="50%"}

This will create and redirect us to our `public-nuget` feed, populated with packages from "NuGet.org". 

![NuGet Feed Overview](/resources/docs/proget-nuget-publicfeed.png){height="" width="50%"}

## Step 4 (Optional): Configure HTTPS/SSL

Another recommended step when setting up ProGet for the first time is to  configure HTTPS on your server. For those who haven't done it before it  may sound daunting, but our page on [configuring HTTPS](/docs/installation/windows/web/https-support) not only explains it clearly but makes it painlessly easy to do so.

![HTTPS Settings](/resources/docs/proget-admin-httpssettings.png){height="" width="50%"}

Note that some clients such as NuGet and Visual Studio may warn about HTTPS primarily to ensure security in web development. These warnings cover issues like SSL/TLS certificates and security recommendations to help developers create secure and compliant web applications.

## Step 5: Connect to Tools (e.g. Visual Studio)

Now that you've created a feed, you can use other ProGet features such as license detection or vulnerability scanning.

To see why license detection is important, see [How to Avoid Costly Lawsuits from Unexpected NuGet License Agreements](https://blog.inedo.com/nuget/avoid-nuget-license-lawsuits/) on our blog.

You can also connect ProGet to Visual Studio as a Package Manager by following the on-screen instructions or reading ["HOWTO: Proxy Packages from NuGet.org in Visual Studio or CLI](/docs/proget/feeds/nuget/howto-nuget-publish). 

![Visual Studio Setup](/resources/docs/proget-nuget-visualstudio.png){height="" width="50%"}
