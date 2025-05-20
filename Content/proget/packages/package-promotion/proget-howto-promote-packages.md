---
title: "HOWTO: Approve and Promote Open-source Packages"
order: 1
---


In ProGet, [package promotion](/docs/proget/packages/package-promotion) is simply a process of copying a package from one feed into another, and keeping a record of that promotion. This is a reliable way to ensure that only approved and verified packages are used in the right environments.

This article will walk you through a [common scenario of promoting open-source NuGet packages](https://blog.inedo.com/nuget/package-approval-workflow). We'll use [NuGet packages](/docs/proget/feeds/nuget) from [NuGet.org](https://nuget.org/packages), along with an `unapproved-nuget` and `approved-nuget` feed, but these steps can apply to any type of package.

:::(Info) (📺 3-minute Video:  Package Promotion with ProGet)
<iframe width="600" height="337" src="https://www.youtube.com/embed/emAWUzrweSg" frameborder="0" allowfullscreen="true"></iframe>
:::

## Step 1: Create new feeds

The first thing we'll be doing is creating two "NuGet" feeds, one for unapproved packages, and the other for approved packages that have been promoted.

We start by selecting "Feed" and [Create New Feed].

![Create New Feed "create-feed"](/resources/docs/proget-feeds-createnewfeed.png){height="" width="50%"}

As we will be using packages from [Nuget.org](https://nuget.org), we select "NuGet (.NET) Packages".

![Create NuGet Feed "create-nuget-feed"](/resources/docs/proget-feeds-nugetselect.png){height="" width="50%"}

## Step 2: Configure feeds

After selecting the feed type, we’ll specify that the feed will connect directly to "NuGet.org".

![Connect Nuget "connect-nuget"](/resources/docs/proget-nuget-connecttoorg.png){height="" width="50%"}

We will then select "Yes, Create Two Feeds", as for a package to be promoted from one feed to another, there must be multiple feeds.

In this example, we will create an `unapproved-nuget` feed where unverified packages from "NuGet.org" will be stored, and an `approved-nuget` feed for our packages to be promoted to.

![Two Feeds "two-feeds"](/resources/docs/proget-feeds-twofeeds.png){height="" width="50%"}

## Step 3: Naming feeds

We then name our feeds as we specified above, and then click [Create Feeds].

![Name Feeds "name-feeds"](/resources/docs/proget-nuget-namefeeds.png){height="" width="50%"}

We are then presented with several options. More information on these can be found in the [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) and [SCA and Continuous Integration (CI)](/docs/proget/sca/builds/proget-sca-ci) documentation.

![Option Select "option-select"](/resources/docs/proget-nuget-newfeedoptions.png){height="" width="50%"}

Finally, we select [Set Feed Features], which will create the feeds and redirect us to our `unapproved-nuget` feed, populated with packages from "NuGet.org".

![Feed Detail "feed-detail"](/resources/docs/proget-nuget-unapprovedfeed.png){height="" width="50%"}

## Step 4: Set Permissions

There are many ways to configure [security access controls for uses and groups](/docs/proget/administration-security) in ProGet. For this example, we want to permit only senior developers to promote packages to `approved-nuget` feed since they're trained to verify the quality, licenses, and vulnerabilities of open-source packages. To ensure this rule, we'll set up a new permission. By default, only administrators have assigned permissions.

To start, we navigate to "Settings" > "Manage Security".

![Manage Security "manage-security"](/resources/docs/proget-settings-managesecurity.png){height="" width="50%"}

We then navigate to the "Tasks / Permissions" tab, listing the currently configured permissions, and select "add permission".

![Tasks/Permissions "tasks-permissions"](/resources/docs/proget-taskspermissions-add.png){height="" width="50%"}

Next, we will fill out the following dialog to give the "Senior Developers" user group permission to "Promote Packages" from the `unapproved-nuget` feed.

![Permit Seniors "permit-seniors"](/resources/docs/proget-nugetunapproved-permitseniordevs.png){height="" width="50%"}

Following the same steps, we will also give the "Developers" user group permission to "View and Download" packages from the `approved-nuget` feed.

![Permit Developers "permit-developers"](/resources/docs/proget-nugetapproved-permitdevs.png){height="" width="50%"}

After saving these two privileges, our task overview page looks like this:

![Overview "overview"](/resources/docs/proget-taskspermissions-nugetadded.png){height="" width="50%"}


* * *

## How to Promote Packages

Promoting packages implies that someone approves a package, often after following an internal process that involves checking the license, vulnerabilities, and quality.

ProGet's integrated license and vulnerability scanning can help by automating the scanning and verification, but there's usually human judgment involved.

### Example: Newtonsoft.Json

Most organizations would find the [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) NuGet package to be acceptable in terms of license, vulnerabilities, and quality. This guide will demonstrate how we would promote this package.

We start by clicking the "version number" (in this case 13.0.3) to direct us to the package page, and then simply select "Promote Package" from the drop-down menu on the right side.

![Promote Package "promote-package"](/resources/docs/proget-nugetunapproved-promoteselect.png){height="" width="50%"}

From here, we fill out the dialog box with any comments, and finally select [Promote]:

![Promote Package in ProGet](/resources/docs/proget-nugetunapproved-promote.png){height="" width="50%"}

Since we configured a package approval workflow earlier, this package can only be promoted to the `approved-nuget` feed. Without that configured, there would be a choice of feeds to promote to.

After clicking promote, the package will now be available in the `approved-nuget` feed for all "Developers" to view and download.

![Approved "approved"](/resources/docs/proget-nugetapproved-feed.png){height="" width="50%"}

Package promotion can also be done using the [Package Promotion API](/docs/proget/packages/package-promotion). The API usage is limited to paid users, however.

## Viewing Promotion History
ProGet keeps a package history that outlines what actions were taken by whom. For example, to view the history of the "Newtonsoft.Json" package, we navigate to the package page and click the "History" tab.

![History "history"](/resources/docs/proget-newtonsoft-promotionhistory.png){height="" width="50%"}

The history tracks over both feeds, and shows us that we promoted this package from `unapproved-nuget` to `approved-nuget`.

:::(Info)
Free users will only be able to see the action taken and the date, and  NOT the user and where the package was promoted to/from.
:::

## Advanced: Repackaging

Package promotion can also be used with [prerelease packages and repackaging](/docs/proget/packages/repackaging). This is best for packages developed in-house, and is done by creating a CI package, repackaging it to an RC package, and then repackaging and promoting it once more to a stable package in a different feed.
