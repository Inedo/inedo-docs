---
title: Creating and Customizing Tasks
keywords: buildmaster, security, permissions, tasks
sequence: 200
---

The built-in tasks are a good starting point, but as you expand BuildMaster within your organization, you may want to customize tasks to help model governance and compliance policies.

A task has a name and description, and is comprised of a set of more granular security attributes that define what principals may or may not do when granted the task. Some attributes may be scoped to an environment, whereas others can only be applied to the system scope. For example, infrastructure (servers and environments) is managed at the system level, so the "Manage Infrastructure" attribute cannot be scoped to a single application or environment.

## Proxy Security Attributes

Any system that allows for granular security attributes has the concept of "granting by proxy", or proxy attributes. For example, even if you only grant permission to create and delete files on a file system, you are still granting edit permission by proxy; a user can simply delete and then create a file as a workaround for not being able to edit it. Likewise, users with only edit permissions can delete by proxy, because they can simply edit the file to have no content.

There is no sensible way for any software to prevent proxy operations, which is why it's important to understand what proxy attributes you may be unintentionally allowing.

## Nonsensical Security Attributes

Granular security attributes can also lead towards creating "nonsensical" policies. For example, giving manage but not view permissions is nonsensical because you can't manage something you can't view. In this case, the behavior is undefined; generally, you'll be able to view the thing, but there are some places where it might not be visible.

While you wouldn't intentionally create such a policy, it might occur if you create multiple overlapping restrictions and permissions across multiple overlapping groups. The best way to mitigate this risk is to carefully plan your use of restrictions.

## Transitive Access

Even with well-planned security restrictions in one part of a system, users can bypass those restrictions from another part of the system that didn't consider the same security restrictions. For example, simply denying view privileges on sensitive files on a network drive is not enough to protect sensitive data; anyone with access to the backups will have transitive access to all those files. Of course, you can encrypt the backups, but then anyone with the decryption key will have transitive access to all the backups.

With BuildMaster, it's especially important to consider transitive access. If BuildMaster can access a production server, and you give users administrative access to BuildMaster, then they can also deploy to that server -- there's just no way around that.

## Security Attribute Reference

Some of the attributes are denoted with a superscript E or A. E indicates that they are scopeable by environment and A indicates that they are scopeable by application (or by extension, application group).

### Admin (Configure BuildMaster)

::: {.attention .best-practice}
This attribute is a proxy for all other attributes, and should be only granted to people you intend to be system administrators.
:::

It is a sort of "administrative catch all" that is used to secure pages in the administration section not covered by other attributes, including security, extensions, and all settings.

### Applications (View<sup>A</sup> & Manage<sup>A</sup>)

View allows users to view everything within an application with the exception of deployment logs and deployment plans.

Manage allows users to do the following:

{.docs}
 - change the application's name, description, or other behavioral settings (build number format, enabling BuildMaster issue tracking)
 - edit configuration variables scoped to the application
 - soft delete (i.e. not purge)

::: {.attention .best-practice}
Note that manage is a proxy attribute for all application-related attributes (pipelines, etc).
:::

### Infrastructure (Manage)

This allows users to create, edit, and delete [infrastructure](/docs/buildmaster/administration/agents-and-infrastructure) (servers, roles, and environments).

::: {.attention .best-practice}
This can provide transitive access to all connected servers and should be granted with extreme care.
:::

### Pipelines (Manage<sup>A</sup>)

This allows users to create, edit, and delete [pipelines](/docs/buildmaster/verification/pipelines). This provides transitive access to deploy to force packages to environments, as a user can simply edit a pipeline to remove any gates or deployment windows.

### Plans (View Contents<sup>A</sup> & Manage<sup>A</sup>)

View allows users to open [deployment plans](/docs/buildmaster/deployments/plans) and view the contents.

::: {.attention .best-practice}
You shouldn't put sensitive information (like passwords) in your plans, so there's no good reason to restrict this.
:::

Manage will allow users to create, edit, and delete deployment plans. Note that this provides transitive access to all servers that a user can deploy to.

### Builds (Create<sup>A</sup>, Deploy<sup>AE</sup>, Force<sup>AE</sup>, Manage<sup>A</sup>, View Deployment Logs/Debug<sup>AE</sup>)

Create allows users to only create new [builds](/docs/buildmaster/builds/overview).

Deploy allows users to promote a build to a stage that it is eligible to deploy to (i.e. passed gates, deployment windows, etc).

Force allows users to bypass these gates.

Manage allows users to upload artifacts, change variables, and change build status.

View Deployment Logs and View Deployment Debug Logs are primarily designed to restrict sensitive data that is consequently logged. The "Debug" logs will hide only the debug-level logs (which is where most tools will log sensitive data).

### Releases (Manage<sup>A</sup>)

This allows users to create [releases](/docs/buildmaster/releases/overview), change variables, and change release status.

### Notes (Manage<sup>A</sup>)

This allows users to create, edit, or delete [release/build notes](/docs/buildmaster/releases/notes).

### Issues (Manage<sup>A</sup>)

This allows users to create, edit, or delete [built-in BuildMaster issues](/docs/buildmaster/verification/issue-tracking), and configure external issue sources for an application.

### Script Assets (Manage<sup>A</sup>, View Contents<sup>A</sup>)

View allows users to open [script assets](/docs/executionengine/components/powershell-and-shell) and view the contents.

::: {.attention .best-practice}
You shouldn't put sensitive information (like passwords) in your scripts, so there's no _good_ reason to restrict this.
:::

Manage will allow users to create, edit, and delete script assets.

::: {.attention .best-practice}
Because deployment plans can already run arbitrary commands, and editing script assets used by plans can enable any behavior, there's little security value in allowing only one of these tasks. However, you may wish to limit by responsibility, and may find this a convenient way to do it.
:::

### Credentials (Manage and View Passwords<sup>E</sup>)

View Passwords will allow users to view sensitive (encrypted) fields on [resource credentials](/docs/buildmaster/administration/resource-credentials).

::: {.attention .best-practice}
This attribute primarily exists to allow some users to view credentials only associated with certain environments.
:::

Manage will allow users to create, edit, delete credentials, as well as view all sensitive (encrypted) fields.

::: {.attention .best-practice}
This could provide transitive access to connected systems, so be very careful when granting this.
:::

### Calendars (Manage<sup>A</sup>)

This allows users to create, edit, and delete system [calendars](/docs/buildmaster/releases/calendars).

### Configuration Files (View<sup>AE</sup>, Edit<sup>AE</sup>, Deploy<sup>AE</sup>)

View allows users to open [configuration files](/docs/buildmaster/deployments/configuration-files) and view the contents for a specific environment.

Edit will allow users to create, edit, and delete configuration file instances in a specific environment.

Deploy will allow users to deploy configuration file instances to a specific environment outside the context of an executing plan.

### SQL Scripts (View<sup>A</sup>, Manage<sup>A</sup>, Deploy<sup>AE</sup>)

View allows users to view [SQL change scripts](/docs/buildmaster/deployments/targets/databases) for an application.

Manage allows users to edit, delete, and change releases for SQL change scripts in an application, but also manage database connections.

::: {.attention .best-practice}
Because database connections are effectively just connection strings, they could point to a database in any environment. This could provide transitive access to DB servers in a different environment if not restricted at the network layer.
:::

Deploy will allow users to deploy SQL change scripts to a specific environment outside the context of an executing plan.

### Change Controls (Manage<sup>AE</sup>)

This allows users to create, edit, perform, or delete [legacy change controls](https://inedo.com/support/kb/1144/buildmaster-legacy-features#change-controls). This permission will likely be removed in a future version of BuildMaster.
