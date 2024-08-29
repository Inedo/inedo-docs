---
title: "HOWTO: Use Approved PowerShell Gallery Packages (Optionally Offline)"
order: 1
---


By using ProGet, teams can restrict access to Microsoft’s public PowerShell module repository, the [PowerShell Gallery](https://www.powershellgallery.com/), ensuring that only approved packages are ever used.

By default, PowerShell attempts to pull and publish from `powershellgallery.com`. That’s not great for teams that work offline or teams that need a package approval workflow in place.

This article will run through a standard example scenario of a company, Kramerica, configuring ProGet to create a [package approval workflow](https://blog.inedo.com/nuget/package-approval-workflow) to ensure only approved packages from the PowerShell Gallery are available for all the operation team to use. This configuration would also allow the feed to be used offline.

## Step 1: Create new feeds

The first thing we need to do is create two "PowerShell" feeds, one for unapproved packages, and the other for approved packages that have been promoted.

We start by selecting "Feeds" and "Create New Feed".

![Create New Feed "create-feed"](/resources/docs/proget-feeds-createnewfeed.png){height="" width="50%"}

Next, we need to select "PowerShell Modules" as we will be using packages from "PowerShell Gallery".

![Create Powershell Feed "create-powershell-feed"](/resources/docs/proget-newfeed-powershellselect.png){height="" width="50%"}

## Step 2: Configure the feeds

After selecting the feed type, we’ll specify that the feed will connect directly to "PowerShell Gallery".

![Connect PowerShell "connect-powershell"](/resources/docs/proget-powershell-connecttoorg.png){height="" width="50%"}

We then select "Yes, Create Two Feeds", as for a package to be promoted from one feed to another, there must be multiple feeds.

In this example, we will create an `unapproved-powershell` feed where unverified packages from "PowerShell Gallery" will be stored, and an `approved-powershell` feed for our packages to be promoted to.

![Two Feeds "two-feeds"](/resources/docs/proget-powershell-twofeeds.png){height="" width="50%"}

## Step 3: Naming our feeds

From here, we name our feeds as specified above, and then click "Create Feeds".

![Name Feeds "name-feeds"](/resources/docs/proget-powershell-namefeeds.png){height="" width="50%"}

We are then presented with several options. More information on these can be found in the [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) documentation.

![Option Select "option-select"](/resources/docs/proget-powershell-newfeedoptions.png){height="" width="50%"}

Finally, we select [Set Feed Features], which will create the feeds, and redirect us to our `unapproved-powershell` feed, now populated with packages from "PowerShell Gallery".

![Feed Detail "feed-detail"](/resources/docs/proget-powershellunapproved-feed.png){height="" width="50%"}

## Step 4: Set Permissions

There are many ways to [configure security access controls for uses and groups](/docs/proget/administration-security) in ProGet. In this example, we want to permit only network engineers to promote packages in the `approved-powershell` feed, since they're trained to check the quality, licenses, and vulnerabilities of open-source packages. To ensure this rule, we'll set up a new permission. By default, only administrators have assigned permissions.

To start, we navigate to "Settings"> "Manage Security".

![Manage Security "manage-security"](/resources/docs/proget-settings-managesecurity.png){height="" width="50%"}

We then navigate to the "Tasks / Permissions" tab, listing the currently configured permissions, and select "add permission".

![Tasks/Permissions "tasks-permissions"](/resources/docs/proget-taskspermissions-add.png){height="" width="50%"}

Next, we will fill out the following dialog to give the "Network Engineers" user group permission to "Promote Packages" from the `unapproved-powershell` feed.

![Permit Engineers "permit-engineers"](/resources/docs/proget-powershellunapproved-permitengineers.png){height="" width="50%"}

Following the same steps, we will also give the "Operations Team" user group permission to "View and Download" packages from the `approved-powershell` feed.

![Permit Operations "permit-operations"](/resources/docs/proget-powershellapproved-permitops.png){height="" width="50%"}

After saving these two privileges, the task overview page looks like this:

![Overview "overview"](/resources/docs/proget-taskspermissions-powershelladded.png){height="" width="50%"}

## How to Use Your PowerShell Feed

To use this newly created feed, we first need to find the endpoint URL on the Feed Overview page and register it as a source.

![Endpoint API "endpoint-api"](/resources/docs/proget-powershellapproved-endpoint.png){height="" width="50%"}

To register this endpoint, we'll use the `Register-PSRepository` cmdlet with the following parameters:

- `-Name` - the name of the approved packages feed
- `-SourceLocation` - the Endpoint URL of the approved packages feed

We can find the required line already set for us by selecting "Set Up PowerShell" on the right-hand side, which will bring up a dialog we can copy from:

![Set Up PowerShell "setup-powershell"](/resources/docs/proget-powershellapproved-setupselect.png){height="" width="50%"}

![Command Line "command-line"](/resources/docs/proget-powershell-setup.png){height="" width="50%"}

However, PowerShell will try to pull modules from the PowerShell Gallery, so we need to unregister it using the `Unregister-PSRepository` cmdlet. That only requires one parameter (`Name`), which is `PSGallery` by default.

### Example

For example, using the feed names we created earlier:

```powershell
Register-PSRepository -Name approved-powershell -SourceLocation https://«host-name»/nuget/approved-powershell
Unregister-PSRepository -Name PSGallery
```

After that, PowerShell will only use the approved packages feed for modules, without needing to access the public PowerShell Gallery.

Note that, if we want to publish packages to this feed, we'll need to also specify the `PublishLocation` parameter as well. This would be the same as `SourceLocation`.