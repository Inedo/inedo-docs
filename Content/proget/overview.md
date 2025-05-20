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

You can install ProGet [quickly and easily from InedoHub](/docs/installation/windows/inedo-hub-installation-guide).

![InedoHub](/resources/docs/windowsinstall-inedohubinstall.png){height="" width="50%"}

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

![Connect Nuget](/resources/docs/proget-nuget-connecttoorg.png){height="" width="50%"}

When you familiarize with ProGet you may want to create two feeds, for unapproved and approved packages respectively. However, for the purpose of demonstration, we will create just the one by selecting "No, Create One Feed". 

![Create One Nuget Feed](/resources/docs/proget-nuget-onenewfeed.png){height="" width="50%"}

We then name our feed. In this case, we will call it public-nuget as it will have public packages from Nuget.org, and then click "Create Feeds". 

![Name One Nuget Feed](/resources/docs/proget-nuget-onefeedname.png){height="" width="50%"}

## Step 4: Select Feed Features 

ProGet will now offer several options to assist you in managing your feed, such as [scanning packages for vulnerabilities](/docs/proget/sca/vulnerabilities) to help you avoid packages unsafe for production use and license detection which will allow you to [block certain licenses](/docs/proget/sca/licenses). 

![Option Select"](/resources/docs/proget-nuget-newfeedoptions.png){height="" width="50%"} 

Finally, we select "Set Feed Features", which will create the feeds and redirect us to our public-nuget feed, populated with packages from "NuGet.org". 

![Name One Nuget Feed](/resources/docs/proget-publicnuget-feed.png){height="" width="50%"}

## Step 5 (Optional): Configure HTTPS/SSL

Another recommended step when setting up ProGet for the first time is to  configure HTTPS on your server. For those who haven't done it before it  may sound daunting, but our page on [configuring HTTPS](/docs/installation/installing-on-iis/installation-windows-https-support) not only explains it clearly but makes it painlessly easy to do so.

![HTTPS Security](/resources/docs/proget-https-settings.png){height="" width="50%"}

Note that some clients such as NuGet and Visual Studio may warn about HTTPS primarily to ensure security in web development. These warnings cover issues like SSL/TLS certificates and security recommendations to help developers create secure and compliant web applications.

## Step 6: Connect to Tools (e.g. Visual Studio)

Now that you've created a feed, you can use other ProGet features such as license detection or vulnerability scanning. 

You can also connect ProGet to Visual Studio as a Package Manager by following the on-screen instructions or reading ["HOWTO: Proxy Packages from NuGet.org in Visual Studio or CLI](/docs/proget/feeds/nuget/howto-nuget-publish). 

![Visual Studio](/resources/docs/proget-publicnuget-visualstudio.png){height="" width="50%"}

 
