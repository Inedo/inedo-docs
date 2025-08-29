---
title: "HOWTO: Use Approved PowerShell Gallery Packages (Optionally Offline)"
order: 1
---


By using ProGet, teams can restrict access to Microsoft’s public PowerShell module repository, the [PowerShell Gallery](https://www.powershellgallery.com/), ensuring that only approved packages are ever used.

By default, PowerShell attempts to pull and publish from `powershellgallery.com`. That’s not great for teams that work offline or teams that need a package approval workflow in place.

This article will run through a standard example scenario of a company, Kramerica, configuring ProGet to create a [package approval workflow](https://blog.inedo.com/nuget/package-approval-workflow) to ensure only approved packages from the PowerShell Gallery are available for all the operation team to use. This configuration would also allow the feed to be used offline.

## Step 1: Create New Feeds

The first thing we need to do is create two "PowerShell" feeds, one for unapproved packages, and the other for approved packages that have been promoted.

### Create Your Unapproved Feed

Here, we'll create a feed to store unverified packages from PowerShell Gallery. Start by selecting "Feeds" and "Create New Feed".

![Create New Feed](/resources/docs/proget-feeds-createnewfeed.png){height="" width="50%"}

Next, we need to select "PowerShell Modules" as we will be using packages from "PowerShell Gallery".

![Create Powershell Feed](/resources/docs/proget-feeds-powershellselect.png){height="" width="50%"}

After selecting the feed type, select "Free/Open Source PowerShell Modules" to connect directly to PowerShell Gallery.

![PowerShell Gallery](/resources/docs/proget-powershell-connecttoorg.png){height="" width="50%"}

From here, name your feed. In this case we'll call ours `unapproved-powershell`.

![Name Unapproved Feed](/resources/docs/proget-powershell-nameunapproved.png){height="" width="50%"}

Select "Create New Feed" to be redirected to your empty `unapproved-powershell` feed, now populated with packages from PowerShell Gallery.

![Unapproved Feed](/resources/docs/proget-powershell-unapprovedfeed.png){height="" width="50%"}

### Create Your Approved Feed

Next, we'll create a validated feed to promote approved packages to.

Once again, select "Feeds", "Create New Feed", and "PowerShell Modules". Now you will be able to select "Validated/promoted PowerShell Modules".

![Approved Packages](/resources/docs/proget-powershell-validated.png){height="" width="50%"}

From here, name your feed. In this case we'll call ours `approved-powershell`.

![Name Approved Feed](/resources/docs/proget-powershell-nameapproved.png){height="" width="50%"}

Select "Create New Feed" to be redirected to your empty `approved-powershell` feed.

![Approved Feed](/resources/docs/proget-powershell-approvedfeed.png){height="" width="50%"}

### Step 2: Promoting Packages

Now both your unapproved and approved PowerShell feeds are created, you can promote verified modules to your approved feed.

First, locate the module and version you wish to promote in your `unapproved-powershell` feed. On the module overview page access the drop-down menu, and select "Promote Package".

![Promote Module](/resources/docs/proget-powershell-promotemodule.png){height="" width="50%"}

You will then be presented with your existing PowerShell feeds and can choose which one to promote the module to. You can also add a comment to provide a reason for promoting the module.

In this case, we'll choose our `approved-powershell` feed, and select "promote".

![Choose Approved Feed](/resources/docs/proget-powershell-choosefeed.png){height="" width="50%"}

Navigating to our `approved-powershell` feed, we can see that the module has been successfully promoted to the feed.

![Approved Module](/resources/docs/proget-powershell-approvedmodule.png){height="" width="50%"}

An alternative option is to set a designated approved PowerShell feed from within your unapproved feed. This way, modules from your unapproved feed can only be promoted to a singular approved feed that you have specified.

Here, we'll demonstrate linking our `unapproved-powershell` feed to our `approved-powershell` feed.

To do this, within your unapproved feed navigate to the "Feed Properties" tab. In the "Other settings" menu navigate to "Promote To Feed" and select "change".

![Promote To Feed](/resources/docs/proget-powershell-feedproperties-promotetofeed.png){height="" width="50%"}

Choose your `approved-powershell` feed from the drop-down menu and select "Save".

![Set Feed](/resources/docs/proget-powershell-feedproperties-setfeed.png){height="" width="50%"}

Now when promoting packages from your `unapproved-powershell` feed, you'll only have the option to promote to the specified approved feed.

![Approved Feed Options](/resources/docs/proget-powershell-singlefeed.png){height="" width="50%"}

## Step 3: Set Permissions

There are many ways to [configure security access controls for uses and groups](/docs/proget/administration-security) in ProGet. In this example, we want to permit only network engineers to promote packages in the `approved-powershell` feed, since they're trained to check the quality, licenses, and vulnerabilities of open-source packages. To ensure this rule, we'll set up a new permission. By default, only administrators have assigned permissions.

To start, we navigate to "Settings"> "Manage Security".

![Manage Security](/resources/docs/proget-admin-managesecurity.png){height="" width="50%"}

We then navigate to the "Tasks / Permissions" tab, listing the currently configured permissions, and select "add permission".

![Tasks / Permissions](/resources/docs/proget-admin-taskspermissions-add.png){height="" width="50%"}

Next, we will fill out the following dialog to give the "Network Engineers" user group permission to "Promote Packages" from the `unapproved-powershell` feed.

![Permit Engineers](/resources/docs/proget-powershell-permitengineers.png){height="" width="50%"}

Following the same steps, we will also give the "Operations Team" user group permission to "View and Download" packages from the `approved-powershell` feed.

![Permit Operations](/resources/docs/proget-powershell-permitoperations.png){height="" width="50%"}

After saving these two privileges, the task overview page looks like this:

![Permissions Overview](/resources/docs/proget-admin-taskspermissions-powershelladded.png){height="" width="50%"}

## How to Use Your PowerShell Feed

To use this newly created feed, we first need to find the endpoint URL on the Feed Overview page and register it as a source.

![PowerShell Endpoint](/resources/docs/proget-powershell-endpoint.png){height="" width="50%"}

To register this endpoint, we'll use the `Register-PSRepository` cmdlet with the following parameters:

- `-Name` - the name of the approved packages feed
- `-SourceLocation` - the Endpoint URL of the approved packages feed

We can find the required line already set for us by selecting "Set Up PowerShell" on the right-hand side, which will bring up a dialog we can copy from:

![Setup Button](/resources/docs/proget-powershell-setupselect.png){height="" width="50%"}

![Setup CMD](/resources/docs/proget-powershell-setupcmd.png){height="" width="50%"}

However, PowerShell will try to pull modules from the PowerShell Gallery, so we need to unregister it using the `Unregister-PSRepository` cmdlet. That only requires one parameter (`Name`), which is `PSGallery` by default.

### Example

For example, using the feed names we created earlier:

```powershell
Register-PSRepository -Name approved-powershell -SourceLocation https://«host-name»/nuget/approved-powershell
Unregister-PSRepository -Name PSGallery
```

After that, PowerShell will only use the approved packages feed for modules, without needing to access the public PowerShell Gallery.

Note that, if we want to publish packages to this feed, we'll need to also specify the `PublishLocation` parameter as well. This would be the same as `SourceLocation`.