---
title: "HOWTO: Approve and Promote Open-source Packages"
order: 1
---


In ProGet, [package promotion](/docs/proget/packages/package-promotion) is simply a process of copying a package from one feed into another, and keeping a record of that promotion. This is a reliable way to ensure that only approved and verified packages are used in the right environments.

This article will walk you through a [common scenario of promoting open-source NuGet packages](https://blog.inedo.com/nuget/package-approval-workflow). We'll use a feed proxing packages from [NuGet.org](https://nuget.org/packages) for "unapproved" packages, and another feed for "approved" packages. We'll use NuGet packages in this example, but these steps can apply to any type of package.

:::(Info) (📺 3-minute Video:  Package Promotion with ProGet)
<iframe width="600" height="337" src="https://www.youtube.com/embed/emAWUzrweSg" frameborder="0" allowfullscreen="true"></iframe>
:::

## Step 1: Create a new feed

The first thing we'll be doing is creating a NuGet feed where unapproved packages from "NuGet.org" are proxied using a [connector](/docs/proget/feeds/connector-overview). If you already have an existing NuGet package feed like this set up, you can skip to [Step 3](#step-3-create-a-second-feed) to create a second feed for promoting your approved packages.

We start by selecting "Feed" and "Create New Feed."

![Create New Feed](/resources/docs/proget-feeds-createnewfeed.png){height="" width="50%"}

As we will be using packages from [Nuget.org](https://nuget.org), we select "NuGet (.NET) Packages".

![Create NuGet Feed](/resources/docs/proget-feeds-nugetselect.png){height="" width="50%"}

## Step 2: Configure and name feed

After selecting the feed type, we’ll specify that the feed will connect directly to NuGet.org.

![Nuget Connector](/resources/docs/proget-opensource-nuget.png){height="" width="50%"}

We then name our feed. In this example, we will name it `public-nuget`, as it will proxy OSS NuGet packages. 

![Name Public Feed](/resources/docs/proget-nuget-namefeed-public.png){height="" width="50%"}

Finally, we select "Create New Feed", which will create and redirect us to our `public-nuget` feed, populated with OSS packages.

![Public Feed Details](/resources/docs/proget-nuget-public-unapprovedfeed.png){height="" width="50%"}

## Step 3: Create a second feed

Now that our `public-nuget` feed has been created, we'll create a second feed for approved packages that have been promoted.

Just like in [Step 1](#step-1-create-a-new-feed), start by selecting "Feed" and then "Create New Feed". Since we'll be promoting packages from our `public-nuget` feed, choose "NuGet (.NET) Packages".

Next, we'll indicate that this feed is for promoted packages by selecting "Validated/promoted NuGet (.NET) Packages".

![Nuget Promoted](/resources/docs/proget-promoted-nuget.png){height="" width="50%"}

Again, we name our feed. This time, we'll name it `approved-nuget`.

![Name Approved Feed](/resources/docs/proget-nuget-namefeed-approved.png){height="" width="50%"}

Select "Create New Feed" to create the `approved-nuget` feed and be redirected to it, ready to receive promoted packages.

![Approved Feed Details](/resources/docs/proget-nuget-approvedfeed-empty.png){height="" width="50%"}

## Step 4: Set Permissions

There are many ways to configure [security access controls for uses and groups](/docs/proget/administration-security) in ProGet. For this example, we want to permit only "Senior Developers" to promote packages to `approved-nuget` feed since they're trained to verify the quality, licenses, and vulnerabilities of open-source packages. To ensure this rule, we'll set up a new permission. By default, only "Administrators" have assigned permissions.

To start, we navigate to "Administration Overview" > "Manage Security".

![Manage Security](/resources/docs/proget-admin-managesecurity.png){height="" width="50%"}

We then navigate to the "Tasks/Permissions" tab, listing the currently configured permissions, and select "add permission".

![Tasks / Permissions](/resources/docs/proget-admin-taskspermissions-add.png){height="" width="50%"}

Next, we will fill out the following dialog to give the "Senior Developers" user group permission to "Promote Packages" from the `public-nuget` feed.

![Permit Senior Developers](/resources/docs/proget-nuget-permitseniordevs.png){height="" width="50%"}

Following the same steps, we will also give the "Developers" user group permission to "View and Download" packages from the `approved-nuget` feed.

![Permit Developers](/resources/docs/proget-nuget-permitdevs.png){height="" width="50%"}

After saving these two privileges, our task overview page looks like this:

![Overview](/resources/docs/proget-admin-taskspermissions-nugetadded.png){height="" width="50%"}

## Step 5: Promote Packages

Promoting packages implies that someone approves a package, often after following an internal process that involves checking the license, vulnerabilities, and quality.

ProGet's integrated license and vulnerability scanning can help by automating the scanning and verification, but there's usually human judgment involved.

### Example: Newtonsoft.Json

Most organizations would find the [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) NuGet package to be acceptable in terms of license, vulnerabilities, and quality. This guide will demonstrate how we would promote this package.

We start by clicking the "version number" (in this case 13.0.3) to direct us to the package page, and then simply select "Promote Package" from the drop-down menu on the right side.

![Select Promote Package](/resources/docs/proget-nuget-promoteselect.png){height="" width="50%"}

From here, we'll select our `approved-nuget` feed and fill out the dialog box with any comments, and finally select "Promote".

![Promote Package in ProGet](/resources/docs/proget-nuget-promotepackage.png){height="" width="50%"}

After clicking "Promote", the package will now be available in the `approved-nuget` feed for all "Developers" to view and download.

![Approved Feed](/resources/docs/proget-nuget-approvedfeed.png){height="" width="50%"}

Package promotion can also be done using the [Package Promotion API](/docs/proget/packages/package-promotion). The API usage is limited to paid users, however.

* * *

## Viewing Promotion History
ProGet keeps a package history that outlines what actions were taken by whom. For example, to view the history of the "Newtonsoft.Json" package, we navigate to the package page and click the "History" tab.

![Promotion History](/resources/docs/proget-nuget-newtonsoft-promotionhistory.png){height="" width="50%"}

The history tracks over both feeds, and shows us that we promoted this package from `public-nuget` to `approved-nuget`.

:::(Info)
Free users will only be able to see the action taken and the date, and  NOT the user and where the package was promoted to/from.
:::

## Advanced: Repackaging

Package promotion can also be used with [prerelease packages and repackaging](/docs/proget/packages/repackaging). This is best for packages developed in-house, and is done by creating a CI package, repackaging it to an RC package, and then repackaging and promoting it once more to a stable package in a different feed.