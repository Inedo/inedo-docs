---
title: "Creating and Customizing Tasks"
order: 3
---

ProGet's built-in security tasks are a good starting point, but as you expand ProGet across your organization, you may want to customize tasks to help model governance and compliance policies.

To customize the tasks available in ProGet, navigate to Admin > Users & Tasks > Tasks, and click [Customize Tasks]. From there, you can add, edit, and delete tasks.

## What is Task in ProGet?
A task has a Name, Description, and a number of Security Attributes.

:::(Warning) (Built-in Tasks)
You can change ProGet's built-in tasks. However, future updates may change your description or add additional attributes as new features are added, so as always, read the upgrade notes before upgrading. ProGet uses the name of the task to determine whether or not it's a built-in task.
:::

### Security Attributes
Security attributes determine what happens when tasks are used in a permission or restriction.

Some attributes are considered feed-scopable, meaning they can be applied to specific feeds. Other attributes can only be applied to the system scope. For example, Scheduled Tasks are managed at the system level (and used by all feeds), the Scheduled Tasks attribute cannot be applied to an individual feed.

If a task can only apply to feeds, then the task can be used in a feed-specific permission or restriction.

###  Attribute Reference 

Some of the attributes are marked with a superscript F, indicating that they are scopable by feed.

####  Administration: Configure ProGet 
:::(Error)
This is a proxy attribute for all other attributes and should only be included in tasks granted to system administrators.
:::
This task is a kind of "administrative catch all" that is used to secure pages in the administration area that are not covered by other attributes, including security, extensions and all settings.

####  Administration: Manage Connectors<sup>F</sup>
:::(Warning)
If a connector is shared by multiple feeds, a user only needs permission for one of those feeds to edit the connector.
:::
This allows users to create, edit, and delete connectors. When used in a feed section, connectors can only be added, edited, and deleted from allowed feeds.

####  Administration: Manage Feeds<sup>F</sup> 

This allows full control over the feed, including changing package stores. It is an administrative access for the feed and is a proxy attribute for all other attributes.

####  Administration: Manage Scheduled Tasks 

Manage allows users to manually run, disable, and change the status of these tasks.

####  Administration: View Scheduled Tasks

View allows users to see the results of scheduled tasks. These do not necessarily have to contain sensitive information, but they can.

#### Licenses: Manage Licenses

Allows users to create, edit, and delete known license types, as well as define licensing rules for feeds and globally. Many packages do not use standard SPDX codes, but instead specify a custom URL (often to their project page or source repository), and it would be practically useless if not allowed to define new known license types or make known license types a feed-specific setting.

#### Credentials:  Manage Credentials  & View Passwords
ProGet uses credentials only for advanced Active Directory configuration.

Unless we extend credentials to other aspects of ProGet, they will likely be removed in a future version of ProGet and replaced with "Configure ProGet".

#### Vulnerabilities:  Assess 

Allows users to create, edit, and delete assessment types, and to assess vulnerabilities.

#### Feeds: Accept Package Promotions<sup>F</sup> 

This attribute allows promoting a package from another feed (the "source" feed) to the feed to which this permission applies. You should also grant the "View" and "Download Package" permissions to the source feed.

#### Feeds: Add  Package <sup>F</sup> 

The Add attribute allows the UI - and API-based addition of packages.

#### Feeds: Delete Package <sup>F</sup>

The Delete attribute allows UI- and API-based deletion of packages.

#### Feeds: Download Package <sup>F</sup>

The Download attribute allows UI- and API-based downloading of packages. This attribute is essentially required for read-only access to a feed. There are some very limited use cases where feed and package metadata can be displayed, but the packages themselves cannot be downloaded (or vice versa).

#### Feeds: Overwrite Package <sup>F</sup> 

The Overwrite attribute allows UI- and API-based overwriting of existing packages.

#### Feeds: Pull Package <sup>F</sup> 

The Pull attribute only allows the use of the "pull package" feature of UI. Allowing only Pull may be useful to build some kind of approval workflow for connector packages.

#### Feeds: Unlist Package <sup>F</sup> 

The Unlist attribute allows UI- unlisting packages from the feed and API.

#### Feeds: View Package <sup>F</sup> 

The View attribute allows UI- and API-based viewing of packages. This attribute is essentially required for read-only access to a feed. There are some very limited use cases where viewing feed and package metadata is allowed, but not downloading the packages themselves (or vice versa).

 ## Task Best Practices

The granular nature of ProGet's security attributes can lead to undesirable results, including granting permissions to users you don't want. When creating or customizing tasks, there are two important concepts to keep in mind: Permission by Proxy and Nonsensical Access.

### Permission by Proxy
Giving someone a "permission by proxy" allows them to perform a possibly undesirable action indirectly or in a roundabout way.

For example, if you create a task that contains only the "Add Package" and "Delete Package" attributes (but not the "Overwrite Package" attribute), then users can indirectly overwrite packages with that task. They'd only need to delete the package first and then add it again.

Another example is the "Configure ProGet" attribute. Users with this permission can change their own permissions, which means they can do virtually anything they want in ProGet. Only a few steps are required.

This isn't necessarily a bad practice. If you're worried about users accidentally performing actions (like overwriting packages), then this restriction can prevent that. However, it's not good for security.

### Nonsensical Access
Granting someone "nonsensical access" means that they can perform sensitive actions (such as editing or deleting), but are restricted from less sensitive actions (such as viewing).

For example, if a task contained only "Manage Feed" but not "View Feed", and a user was only granted permission to perform that task, the behavior in ProGet would be very strange. Technically, he could view the feed on the "manage" pages, but he'd probably get errors on the "view-only" pages.

Tasks with "nonsensical access" aren't necessarily bad either. You can use tasks to create multiple, overlapping restrictions and permissions for multiple overlapping groups.

