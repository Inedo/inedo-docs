---
title: "HOWTO: Add ProGet as Package Source in Visual Studio"
order: 4
---


Connecting ProGet with Visual Studio is a great way to increase the security of the most widely used integrated development environment for Windows developers. ProGet acts as a NuGet Package Manager for Visual Studio, as ProGet can detect package licenses, scan packages for vulnerabilities, and control promotion between feeds.

This guide describes how to connect Visual Studio and ProGet and how to set up authentication for a NuGet feed connected to ProGet in Visual Studio.

:::(Info)
To get started, you’ll need a NuGet feed already set up in ProGet. We recommend reading our[ guide about approving and promoting packages](/docs/proget/packages/package-promotion/proget-howto-promote-packages) too. 
:::

## Step 1: In ProGet, copy your API Endpoint URL

Navigate to the top of the desired feed and copy the API endpoint URL at the top right of the page.

![proget-apiendpointurl-highlighted.png](/resources/docs/proget-apiendpointurl-highlighted.png)

## Step 2: In Visual Studio, open Package Manager Settings

Navigate *Tools > NuGet Package Manager > Package Manager Settings*

## Step 3: Disable nuget.org in your Package Manager Settings

Uncheck the box to the left of *nuget.org*

![visualstudio-packagesources-highlightednugetorg.png](/resources/docs/visualstudio-packagesources-highlightednugetorg.png)

This prevents Visual Studio from scanning both NuGet.org and ProGet for packages. If you configure Visual Studio to search only ProGet instead of NuGet.org and ProGet, you avoid problems such as bad licenses, vulnerable packages, and [dependency confusion](https://blog.inedo.com/software-supply-chain-security/three-things) in your packages when you use multiple sources.

## Step 4: Create a new package source

1. Click the green + in the top right of the window.
2. Name the new package source. 
3. Paste in your API endpoint URL from Step 1

![visualstudio-packagesources-configureproget.png](/resources/docs/visualstudio-packagesources-configureproget.png)

## Step 5: Save your package source configuration

1. Click the  [Update] button.
2. Click the [OK] button.

![visualstudio-packagesources-updateandok.png](/resources/docs/visualstudio-packagesources-updateandok.png)

Visual Studio and ProGet are now connected.

:::(Warning) (Be sure to click [Update] prior to clicking [OK])
If you click [OK] without clicking [Update] your package source configuration will not be saved in Visual Studio.
:::

## Step 6: Check the connection

To confirm the connection, in Visual Studio, right-click on a project in the Solution Explorer and select “Manage NuGet Packages…” from the menu.

In the Package Manager window, your feed will populate under “Browse”

![visualstudio-connectedprogetfeed.png](/resources/docs/visualstudio-connectedprogetfeed.png)

## Adding Authentication to a NuGet Feed

Teams can require authentication for feeds in ProGet, which means that users must log in to their package source in ProGet. To do this, perform the following steps:

### Step A: In ProGet, remove anonymous access from ProGet

Before you can set up an authenticated feed in Visual Studio, you must be logged in as an administrator in ProGet.

Once you're logged in, navigate to *Settings > Users and Tasks > Tasks*

In ProGet, "Anonymous" is an administrator by default. This is to facilitate the initial setup of ProGet on a server. If you haven't already done so, remove the anonymous access.

Then remove the other permissions that Anonymous has, such as viewing and downloading feeds.

:::(Info) 
Read [our guide on restrictions and permissions](/docs/proget/administration-security/proget-howto-configure-permissions-and-restrictions-on-feeds) to learn more granular details about users and tasks. 
:::

### Step B: Create a Personal API Key

Under the user icon in the top-right of ProGet, select “Personal API Keys”

At the bottom of the page, click [Create Personal API Key]

![proget-personalapikey-create.png](/resources/docs/proget-personalapikey-create.png)

In the pop-up, type an appropriate display name and click [Create API Key]

Then, click on the display name and the pop-up will appear again, this time with the Personal API key generated.

![proget-createpersonalapikey.png](/resources/docs/proget-createpersonalapikey.png)

Copy the Personal API key for Step C.

### Step C: In Visual Studio, connect to the server

Repeat step 6 above and check the connection. This time a pop-up window will appear asking you to connect to the server.

Enter "api" as the user name.

As the password, add the personal API key from step B.

![visualstudio-connecttoserver-inputted.png](/resources/docs/visualstudio-connecttoserver-inputted.png)

Click [OK]

You’ve now configured an Authenticated NuGet feed in Visual Studio. 

## Troubleshooting
### Authentication Error

An error may occur when trying to browse the authenticated NuGet feed in Visual Studio.

![visualstudio-nugetfeed-error.png](/resources/docs/visualstudio-nugetfeed-error.png)

The window does not fill with packages and the error list says "API Key ... does not exist"

There may have been an error entering the personal API key while connecting to the server, or the API key may have been deleted in ProGet.

**To resolve**, in Visual Studio, close all your instances.

Then, in Windows, open Control Panel and navigate to Credential Manager.

![windows-credentialmanager.png](/resources/docs/windows-credentialmanager.png)

Under Windows Credentials, delete the one named as the ProGet host (in this demonstration, *3.144.230.132*)

![windows-credentialmanager-windowscredentials.png](/resources/docs/windows-credentialmanager-windowscredentials.png)

Under Generic Credentials, find and remove the one named “VSCredentials_<progethost>” (in this demonstration, *VSCredentials_3.144.230.132*)

![windows-credentialmanager-genericcredentials.png](/resources/docs/windows-credentialmanager-genericcredentials.png)

Now, In Visual Studio open your instance and navigate to the Package Manager again as in Step 6. 

You will be prompted again to enter a Personal API key, as in Step C. 

![visualstudio-connecttoserver.png](/resources/docs/visualstudio-connecttoserver.png)

Repeat Step C to reconnect to the authenticated NuGet feed. 

