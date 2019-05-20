---
title: Package Promotion
subtitle:  What is Package Promotion and Why Should I use it.
keywords: package promotion, packages, 3rd party, universal package, deployments
sequence: 600
---

Package Promotion is the process of promoting packages between feeds to ensure that only approved and verified packages are used in the right environments, such as production. 

In ProGet, packages can be promoted from one feed to another and tracked throughout the process. This is useful for maintaining feeds with separate levels of quality (such as Development, Production, etc), and also to easily curate selected packages from an external source to an approved repository.
 
## Package Promotion Security 
Promote Packages is a [built-in task](/support/documentation/proget/administration/security) in ProGet. This task Allows access to promote packages to a specified feed of the same feed type. Users granted this task should also be granted at least the View & Download Packages task for the source feed. 

You can also create a [Custom Task](/support/documentation/proget/administration/security/creating-tasks) page by going to `Administration > Users & Tasks > Customize Tasks`. To give or deny users/groups permissions to this task go to the Users & Tasks page.

## What Packages Can Be Promoted?

- You will need two feeds of the same type, either [universal package format](/support/documentation/various/universal-packages/universal-feeds-package-ref/package-format) or a third party format (such as NuGet, npm, Ruby Gems, and more)

- Generally speaking, all packages can be promoted with only a few exceptions. 
    - Maven packages do not support promotion, and;
    - Connector packages can’t inherently be promoted, they will need to first be pulled into a feed before they can be elligible for promotion.

## How to Promote a Package
There are two ways to promote a package, using the Promote Package button in the Package Versions page in ProGet, or an API call.

Check out this [short video](https://www.youtube.com/watch?v=0uVDUt0wMjM) explaing the steps in ProGet.

To promote a package via the **API** you will first need to generate an API key that   grants access to Package Promotion. Then visit [Package Promotion Endpoint](/support/documentation/proget/reference/api/package-promotion) guide in our [ProGet API Reference](/support/documentation/proget/reference/api) section to familiarize yourself on how to work with the ProGet APIs and the Promote Packages endpoint. 


## Track Your Package Promotions
In the Promotions tab you will obviously have your Promote Package button, but you will also see the history of the package promotions. The history will show

- From Feed: the originating feed name
- To Feed: the destination feed name
- Date:  when the promotion occured
- User: who promoted the package
- Comments: any comments left by the promoter


|From Feed|To Feed|Date|User|Comments
|-----|-----|------|-----|----
NugetBeta|NugetProd|1/2/2019|Admin|Ready for production
NugetTest|NugetBeta|11/22/2018|Admin|Ready for beta


## Best Package Promotion Practices

The primary benefit of using multiple feeds is **package organization**. Tn theory you could put all packages of the same type (NuGet, Universal, etc) into a single feed of that type. However, there are millions of packages out there and maintaining them in one feed can becoming overwhelming very quickly. 

You can organize package feeds in a variety of ways. For instance, you may want to segment your feeds by the groups or teams that should be using them. This can help a team focus on only the packages they need without being burdoned by packages that they don't. 

Another way to organize feeds is by Quality (Untested, Production-ready, etc). Packages move across development and testing pipelines much like any other software. ProGet is a great way to create different feeds for different stages of your pipeline. 

Configurations like security and storage can also be a determining factor on how to organize your various feeds. ProGet allows you to place [Security and Accees Controls](/support/documentation/proget/administration/security) to feeds and give teams different access to different feeds. For example, perhaps you’ll only want Lead Engineers to be able to publish packages to the Production Feed.
