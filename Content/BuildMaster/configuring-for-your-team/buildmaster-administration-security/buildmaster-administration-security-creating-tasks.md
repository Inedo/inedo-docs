---
title: "Creating and Customizing Tasks"
order: 1
---

BuildMaster's built-in security tasks are a good starting point, but as you expand within your organization, you may want to customize tasks to help model governance and compliance policies.     

To customize the tasks available in BuildMaster, navigate to Admin > Users & Tasks > Tasks, and click [Customize Tasks]. From there, you can add, edit, and delete tasks.

## What is Task in BuildMaster?
A task has a Name, Description, and a number of Security Attributes.

:::(Warning) (Built-in Tasks)
You can modify BuildMaster's built-in tasks. However, future updates may change your description or add additional attributes as features are added; so, as always, make sure to read the upgrade notes before upgrading. BuildMaster uses the task's Name to determine if it's a built-in task or not.
:::

### Security Attributes
Security Attributes determine what will happen when the tasks is used in a permission or restriction.

Some attributes are considered application-scopable and/or environment-scopeable, which means they can be applied to specific applications and/or environments. Other attributes can only be applied to the system scope. For example, infrastructure (servers and environments) is managed at the system level, so the "Manage Infrastructure" attribute cannot be scoped to a single application or environment.

If a task has only application-scopeable tasks, then the task may be used in a application-specific permission or restriction. The same holds true with environment-scopable tasks.

## Security Attribute Reference

Some of the attributes are denoted with a superscript E or A. E indicates that they are scopeable by environment and A indicates that they are scopeable by application (or by extension, application group).

### Administration: Configure BuildMaster

::: (info) 
This attribute is a proxy for all other attributes, and should be only granted to people you intend to be system administrators.
:::

It is a sort of "administrative catch all" that is used to secure pages in the administration section not covered by other attributes, including security, extensions, and all settings.

### Administration: Manage Applications

Allows users to create, deactivate, and purge applications.

### Applications: View<sup>A</sup>

View allows users to view everything within an application with the exception of deployment logs, Git source control, script contents, and configuration file instances.

### Applications: View Source<sup>A</sup>

View allows users to view Git repositories in an application.

### Applications: Manage<sup>A</sup>
Manage allows users to do the following:

 - change the application's name, description, or other behavioral settings (build number format, enabling BuildMaster issue tracking)
 - edit configuration variables scoped to the application
 - soft delete (i.e. not purge)

:::  (info)
Note that manage is a proxy attribute for all application-related attributes (pipelines, etc).
:::

### Builds: Add Notes<sup>A</sup>
This allows users to create [release/build notes](/docs/buildmaster/modeling-your-applications/buildmaster-releases/buildmaster-applications-releases-notes).

### Builds: Create<sup>A</sup>
Create allows users to only create new [builds](/docs/buildmaster/builds-continuous-integration/buildmaster-builds).

### Builds: Deploy<sup>A E</sup>
Deploy allows users to promote a build to a stage that it is eligible to deploy to (i.e. passed gates, deployment windows, etc).

### Builds: Edit Notes<sup>A</sup>
This allows users to edit or delete [release/build notes](/docs/buildmaster/modeling-your-applications/buildmaster-releases/buildmaster-applications-releases-notes).

### Builds: Force<sup>A E</sup>
Force allows users to bypass gates.

### Builds: Manage<sup>A</sup>
Manage allows users to upload artifacts, change variables, and change build status.

### Builds: View Deployment Debug Logs<sup>A E</sup>
The "Debug" logs will hide only the debug-level logs (which is where most tools will log sensitive data).

### Builds: View Deployment Logs<sup>A E</sup>
View Deployment Logs and View Deployment Debug Logs are primarily designed to restrict sensitive data that is consequently logged. 


### Calendars: Manage<sup>A</sup>

This allows users to create, edit, and delete system [calendars](/docs/buildmaster/modeling-your-applications/buildmaster-releases/buildmaster-applications-releases-calendars).


### Configuration Files: View Instance<sup>A E</sup>
View allows users to open [configuration files](/docs/buildmaster/deployment-continuous-delivery/buildmaster-applications-configuration-files) and view the contents for a specific environment.

### Configuration Files: Edit Instance<sup>A E</sup>
Edit will allow users to create, edit, and delete configuration file instances in a specific environment.

### Configuration Files: Deploy<sup>A E</sup>
Deploy will allow users to deploy configuration file instances to a specific environment outside the context of an executing plan.

### Credentials: Manage <sup>A E</sup>
Manage will allow users to create, edit, delete credentials, as well as view all sensitive (encrypted) fields.

:::  (info) 
This could provide transitive access to connected systems, so be very careful when granting this.
:::

### Credentials: View Passwords<sup>A E</sup>
View Passwords will allow users to view sensitive (encrypted) fields on [resource credentials](/docs/buildmaster/configuring-for-your-team/buildmaster-administration-resource-credentials).

:::  (info) 
This attribute primarily exists to allow some users to view credentials only associated with certain environments.
:::

### Infrastructure; Manage

This allows users to create, edit, and delete [infrastructure](/docs/buildmaster/administration-agents-and-infrastructure/buildmaster-servers) (servers, roles, and environments).

:::  (info) 
This can provide transitive access to all connected servers and should be granted with extreme care.
:::


### Pipelines: Manage<sup>A</sup>

This allows users to create, edit, and delete [pipelines](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines). This provides transitive access to deploy to force packages to environments, as a user can simply edit a pipeline to remove any gates or deployment windows.


### Releases: Manage<sup>A</sup>

This allows users to create [releases](/docs/buildmaster/modeling-your-applications/buildmaster-releases), change variables, and change release status.

### Releases: Manage Issues <sup>A</sup>

This allows users to create, edit, or delete [built-in BuildMaster issues](/docs/buildmaster/modeling-your-applications/buildmaster-applications-issue-tracking), and configure external issue sources for an application.

### Scripts: View Contents<sup>A</sup>
View allows users to open scripts and view the contents.
:::  (info)
You shouldn't put sensitive information (like passwords) in your plans, so there's no good reason to restrict this.
:::

### Scripts: Manage<sup>A</sup>

Manage will allow users to create, edit, and delete scripts. Note that this provides transitive access to all servers that a user can deploy to.


### SQL Scripts: View Contents<sup>A</sup>
View allows users to view [SQL change scripts](/docs/buildmaster/deployment-continuous-delivery/buildmaster-ci-cd-deployment-databases) for an application.

### SQL Scripts: Manage<sup>A</sup>
Manage allows users to edit, delete, and change releases for SQL change scripts in an application, but also manage database connections.

:::  (info) 
Because database connections are effectively just connection strings, they could point to a database in any environment. This could provide transitive access to DB servers in a different environment if not restricted at the network layer.
:::

### SQL Scripts: Deploy<sup>AE</sup>
Deploy will allow users to deploy SQL change scripts to a specific environment outside the context of an executing plan.


## Task Best Practices

The granular nature of BuildMaster's security attributes may yield to undesired results, including giving users permissions that you don't want. When creating or customizing tasks, there are three important concepts to keep in mind: Permission by Proxy, Nonsensical Access, and Transitive Access.


### Proxy Security Attributes

Any system that allows for granular security attributes has the concept of "granting by proxy", or proxy attributes. For example, even if you only grant permission to create and delete files on a file system, you are still granting edit permission by proxy; a user can simply delete and then create a file as a workaround for not being able to edit it. Likewise, users with only edit permissions can delete by proxy, because they can simply edit the file to have no content.

There is no sensible way for any software to prevent proxy operations, which is why it's important to understand what proxy attributes you may be unintentionally allowing.

### Nonsensical Security Attributes

Granular security attributes can also lead towards creating "nonsensical" policies. For example, giving manage but not view permissions is nonsensical because you can't manage something you can't view. In this case, the behavior is undefined; generally, you'll be able to view the thing, but there are some places where it might not be visible.

While you wouldn't intentionally create such a policy, it might occur if you create multiple overlapping restrictions and permissions across multiple overlapping groups. The best way to mitigate this risk is to carefully plan your use of restrictions.

### Transitive Access

Even with well-planned security restrictions in one part of a system, users can bypass those restrictions from another part of the system that didn't consider the same security restrictions. For example, simply denying view privileges on sensitive files on a network drive is not enough to protect sensitive data; anyone with access to the backups will have transitive access to all those files. Of course, you can encrypt the backups, but then anyone with the decryption key will have transitive access to all the backups.

With BuildMaster, it's especially important to consider transitive access. If BuildMaster can access a production server, and you give users administrative access to BuildMaster, then they can also deploy to that server -- there's just no way around that.

