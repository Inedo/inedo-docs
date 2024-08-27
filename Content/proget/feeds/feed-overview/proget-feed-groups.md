---
title: "Multiple Feeds & Groups"
order: 1
---

ProGet supports the configuration of multiple feeds and optionally the grouping of these feeds into feed groups.
 
##  Multiple Feeds 

The main advantage of using multiple feeds is **package organization**. In theory, you could put everything in a single feed, but there are millions of packages, and managing them in a single feed can get overwhelming very quickly.

You can organize package feeds in a number of ways. For example, you may want to segment your feeds by groups or teams that should use them. This way, a team can focus only on the packages they need without being weighed down by packages they don't need.

Another way to organize feeds is by quality ( Untested, Production-ready, etc.). Configurations such as security and storage can also be a deciding factor in organizing your various feeds. With ProGet, you to place [Security and Access Controls](/docs/proget/administration-security) to feeds and give teams different access to different feeds. For example, you may want Lead Engineers to be able to publish packages to the Production Feed.

## Feed Groups

As of ProGet v6, feeds can be placed in different feed groups, which can be helpful:

* Visual grouping of related applications in the user interface
* Simplifying permissions for all feeds in the group
* Allowing the creation of feeds in a specific group

Feed groups can be created on the Manage Feed page by entering the name of a group that doesn't already exist. You can also configure feeds and their groups at Admin > Feed Groups.

:::(Info)
See our step-by-step guide on [how to create self-service feeds and manage feed groups.](/docs/proget/administration-security/proget-howto-self-service-feeds)
:::