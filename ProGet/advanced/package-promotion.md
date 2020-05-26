---
title: Package Promotion
subtitle:  What is Package Promotion and Why Should I use it.
keywords: package promotion, packages, 3rd party, universal package, deployments
sequence: 600
show-headings-in-nav: true
---

Package Promotion is the process of promoting packages between feeds to ensure that only approved and verified packages are used in the right environments, such as production. 

In ProGet, packages can be promoted from one feed to another and tracked throughout the process. This allows you to build a promotion pipeline that allows you maintain separate levels of quality in different feeds (such as Development, Production, etc). It also enables you to easily curate selected packages from an external source to an approved repository.  


::: {.attention .best-practice}
This feature is limited in ProGet Free. Compare [features by edition](/docs/proget/administration/license) or [request a quote](https://inedo.com/proget/pricing/request-quote). {.info}
:::

## How to Promote a Package {#how-to-promote data-title="How To Promote Packages"}
There are two ways to promote a package, using the Promote Package button in the Package Version page in ProGet, or an API call. Any packages that are promoted to a target feed are __not__ deleted from the source feed.

### Creating a Validated/Promoted Package Feed {#promotion-feed data-title="Creating a Validated/Promoted Feed"}

Any feed can be used as a validated/promoted package feed, but best practice is to create a separate feed that contains only promoted internal packages and verified external packages. When creating your new feed, select the _validated/promoted_ feed usage type.

#### Promoting Packages Using The Package Version Page {#promote-using-package-version-page data-title="Promote Using The UI"}

To promote a package using the Package Version page, first navigate to the feed, package, and version you would like to promote.  Hover over the _▼-button_ in the upper right corner and click the _Promote Package_ button.

::: {.attention .best-practice}
Package Promotion via the package version page is limited in ProGet Free. When using ProGet Free, you can promote packages, but only through this page and you won't be able to see who did it, when, or why. {.info}
:::

#### Promoting Packages Using API {#promote-using-api data-title="Promote Using The API"}

To promote a package via the API you will first need to generate an API key that grants access to Package Promotion. Then visit our [Package Promotion Endpoint](/docs/proget/reference/api/package-promotion) guide in our [ProGet API Reference](/docs/proget/reference/api) section to familiarize yourself on how to work with the ProGet APIs and the Promote Packages endpoint.

::: {.attention .best-practice}
Package promotion via the API is not availabile in ProGet Free.{.info}
:::

### Creating a Package Promotion Pipeline {#create-promotion-pipeline data-title="Creating a Package Promotion Pipeline"}

A package promotion pipeline helps you to enforce the process of elevating packages between your different feeds/environments by selecting a specific target feed. Once a target feed is selected to promote to, both the Package Version Page and the [Package Promotion Endpoint](/docs/proget/reference/api/package-promotion) will only allow you to promote packages to the selected target feed.

To configure the pipeline, you will need to:
1. Navigate to the source Feed containing the packages to be promoted  
2. Click on the _Manage Feed_ button in the upper right corner of the page
3. Click the _configure_ link to the right of the _Promote To Feed_ property
4. Select the target promotion feed

**Note:** A validated/promoted package feed can be a source feed for a different validated/promoted package feed.  This allows you to keep environments separated into different feeds and enforce an elevation process across feeds.

## Package Promotion Security {#security data-title="Package Promotion Security"}

Promote Packages is a [built-in task](/docs/proget/administration/security) in ProGet. This task Allows access to promote packages to a specified feed of the same feed type. Users granted this task should also be granted at least the View & Download Packages task for the source feed. 

You can also create a [Custom Task](/docs/proget/administration/security/creating-tasks) page by going to `Administration > Users & Tasks > Customize Tasks`. To give or deny users/groups permissions to this task go to the Users & Tasks page.

## Track Your Package Promotions {#tracking data-title="Tracking Package Promotions"}

Package promotion tracking is viewable in the _History_ tab on the Package Version page.  The history will show a _Promoted_ action that includes:
- When the promotion occurred
- Who promoted the package
- The source feed
- The target feed
- Any comments left by the promoter

|Action|On|Detail
|-----|-----|------
Promoted|5/14/2020 1:30:15 PM by Admin|from devnuget to rcnuget<br/>Ready for testing
Promoted|5/15/2020 1:30:15 PM by API|from rcnuget to prodnuget<br/>Release 12.0.5


