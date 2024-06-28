---
title: "HOWTO: Filter Packages by License"
order: 1
---

ProGet makes it easy to allow, block, and filter open-source licenses, ensuring compliance and mitigating legal risks in your software development projects. 

:::(Warning) (⚠ Legal Risks of Open Source Packages)
Nearly all open-source packages that you'll find on NuGet.org, npmjs.org, etc. have a license agreement that will legally bind the company to the package author's terms. Some licenses (like GPL-3) are not appropriate for closed-source software, and can put the company at serious legal risk. See [How to avoid Costly Lawsuits from Unexpected Licenses](https://blog.inedo.com/nuget/how-to-avoid-costly-lawsuits-from-unexpected-nuget-license-agreements){target="_blank"} to learn more.
:::

In this article we will look at controlling open-source license usage in ProGet, and how you can block or allow licenses depending on your situational needs.

:::(Info) (📺 3-minute Video:  Filtering Packages by License)
<iframe width="600" height="337" src="https://www.youtube.com/embed/UDdOQxuu0b0" frameborder="0" allowfullscreen="true"></iframe>
:::

## Controlling Open-Source Package License Use

You can take two different approaches in ProGet when controlling how you allow, block and filter the open-source licenses of your development project.

### Blocking Unwanted Licenses (Blacklisting)

Often referred to as "Blacklisting", this will allow downloads of all packages except the ones with licenses you explicitly Prohibit". This is regarded as effective when targeting and blocking specific licenses, especially those known to have a history of causing legal or compliance issues. 

It's also simpler and easier to manage, allowing you to progressively update the list as you encounter and become more aware of existing or new licenses that need to be restricted. 

### Allowing Only Specific Licenses (Whitelisting)

In contrast, often referred to as "Whitelisting", this will prohibit downloads of all packages except the ones with licenses you explicitly allow. This is far more efficient when your organization has very strict licensing policies and wants to ensure air-tight compliance in your production. 

It also encourages greater control over your production environment as you define what is used, avoiding the risk of developers using unapproved licenses. 

This guide will walk you through applying each approach in ProGet. 

## How to Block Unwanted Licenses (Blacklisting) 

In this scenario, we want to take a list of licenses and decide on licenses we want to block, prohibiting downloading and production use. We'll start with "GPL 3.0" due to it's legal risks.

### Step 1: Navigate to License 

Navigate to "Reporting & SCA" > "Licenses" > and select "Manage License Types and Rules".

![proget-licenses-manage.png](/resources/docs/proget-licenses-manage.png){height="" width="50%"}

From here we want to locate the "GPL 3.0" license, and select it. 

![proget-licenses-gpl.png](/resources/docs/proget-licenses-gpl.png){height="" width="50%"}

### Step 2: Block License 

Now from "Global Rule", select "Block Licenses". This will prevent GPL 3.0 from being downloaded or used from any feed in your ProGet instance. 

![proget-licenses-blocklicense.png](/resources/docs/proget-licenses-blocklicense.png){height="" width="50%"}

### Step 3: Confirm Restriction 

To confirm this, we will take a look at "Accord.MachineLearning 3.8.0", which uses the GPL 3.0 license. Navigate to "Feeds" and select a NuGet feed. For this demonstration, we will use a feed we have created named `public-nuget`. From here search for "Accord.MachineLearning" and select the relevant package. 

![proget-feed-accordmachinelearning.png](/resources/docs/proget-feed-accordmachinelearning.png){height="" width="50%"}

We can now see that it is blocked as a result of our actions. 

![proget-package-blocked.png](/resources/docs/proget-package-blocked.png){height="" width="50%"}

#### Overriding at Feed Level

Overriding at feel level allows us to configure unique rules for feeds of our choosing. In this case, it will allow us to permit the downloading and use of packages using licenses that have otherwise been blocked globally. 

In this scenario, "Project ABC," which uses a `nuget-project-abc` NuGet feed, is licensed under GPL 3.0. Therefore, packages licensed under GPL can be approved for use in this project.

As with Step 1 above, navigate to the "GPL 3.0" license and select it. Now check "Override at feed-level" and from "Allow in feeds:" select `nuget-project-abc`. This will permit downloading and use of packages that use the GLP 3.0 license. 

![proget-licenses-blocklicense-override.png](/resources/docs/proget-licenses-blocklicense-override.png){height="" width="50%"}

We can confirm this by navigating to the `nuget-project-abc` feed and selecting the "Accord.MachineLearning" package. We can now see that it is allowed as a result of our actions.

![proget-package-allowed-override.png](/resources/docs/proget-package-allowed-override.png){height="" width="50%"}

## How to Allow only Specific Licenses (Whitelisting) 

In this scenario we want to take a list of licenses, begin by blocking them globally, and then decide on licenses we want to allow, permitting downloading and production use. We'll start with the "MIT" license as it's generally regarded as acceptable.
 
### Step 1: Block All Licenses by Default 

In ProGet, all licenses are allowed by default when a feed is initially set up, so we will need to change this first. Start by navigating to "Reporting & SCA" > "Licenses" > and selecting "Manage License Types and Rules".

![proget-licenses-manage.png](/resources/docs/proget-licenses-manage.png){height="" width="50%"}

From here select "edit default rules". Then from "Global Rule" select "Block downloads by default".

![proget-licenses-blockall.png](/resources/docs/proget-licenses-blockall.png){height="" width="50%"}

You will now see that all licenses listed are blocked by default. 

![proget-licenses-allblocked.png](/resources/docs/proget-licenses-allblocked.png){height="" width="50%"}

### Step 2: Allow License 

Next, we will allow the MIT license. From the licenses page find and select the "MIT" license. 

![proget-licenses-mit.png](/resources/docs/proget-licenses-mit.png){height="" width="50%"}

Then from "Global Rule" select "Allow Licenses". This will allow packages with the MIT license to be downloaded and used. 

![proget-licenses-allowlicense.png](/resources/docs/proget-licenses-allowlicense.png){height="" width="50%"}

### Step 3: Confirm Permission 

To confirm this, we will take a look at "Newtonsoft.json", which uses the MIT license. Navigate to "Feeds" and select a NuGet feed. For this demonstration, we will use a feed we have created named `public-nuget`. From here select the "Newtonsoft.json 13.0.3" package, which will normally be one of the first in the feed, or search for it if necessary. 

![proget-feed-newtonsoft.png](/resources/docs/proget-feed-newtonsoft.png){height="" width="50%"}

We can now see that it is allowed as a result of our actions. 

![proget-package-allowed.png](/resources/docs/proget-package-allowed.png){height="" width="50%"}

#### Overriding at feed level

In the same way as detecting and blocking above, overriding at feel level is possible when allowing approved licenses. In this case, it will allow us to prohibit the downloading and use of packages using licenses that have otherwise been allowed globally. 

In this scenario, GPL licenses are globally approved. However, for "Project XYZ," which uses a `nuget-project-xyz` NuGet feed, is explicitly prohibited from using these licenses. Therefore, the "GPL-3.0" license must be blocked for this particular project.
 
Navigate to the "GPL 3.0" license and select it. Now check "Override at feed-level" and from "Block in feeds:" select `nuget-project-xyz`. This will block downloading and use of packages that use the GLP 3.0 license. 

![proget-licence-override-block.png](/resources/docs/proget-licence-override-block.png){height="" width="50%"}

We can confirm this by navigating to the `nuget-project-xyz` feed and selecting the "Accord.MachineLearning 3.8.0" package. 

We can now see that it is blocked as a result of our actions. 

![proget-package-blocked-override.png](/resources/docs/proget-package-blocked-override.png){height="" width="50%"}

## Section 3: Troubleshooting 

### What if a Package Has No License? 

When handling packages in a feed, you may experience two cases of packages without any assigned license. 

**Case 1: URL-Based Licenses**

These are packages with URL-based licenses. An example of this is "Aspose.Words 23.9.0". When viewed in ProGet, it will appear like this: 

![proget-nolicense-embedded.png](/resources/docs/proget-nolicense-embedded.png){height="" width="50%"}

For these packages, you will need to access their related website to view the license. 

![proget-licenses-aspose-url.png](/resources/docs/proget-licenses-aspose-url.png){height="" width="50%"}

In this case, "Aspose.Words" has it's own license, meaning you will need to assign it a custom license in ProGet. Do this by selecting "License". 

Then select "Assign License", type in a name for the license, and select "Save". 

![proget-license-url-assign.png](/resources/docs/proget-license-url-assign.png){height="" width="50%"}

**Case 2: Embedded Licenses**

These are packages with embedded licenses. An example of this is " Accord.MachineLearning 3.8.0". When viewed in ProGet, it will appear like this: 

![proget-nolicense-url.png](/resources/docs/proget-nolicense-url.png){height="" width="50%"}

For these packages, you will need to view the license via the link on it’s page. 

Navigate to the "Accord.MachineLearning 3.8.0" package in a NuGet feed, and click on the link to read the license. Here it states that it uses the "GPL 3.0" license. 

![proget-license-embedded-assign.png](/resources/docs/proget-license-embedded-assign.png){height="" width="50%"}

Select "Assign License Type to Custom URL", then search for "GPL 3.0" from the list and select "Save". 

![proget-licenses-embedded-assign.png](/resources/docs/proget-licenses-embedded-assign.png){height="" width="50%"}
