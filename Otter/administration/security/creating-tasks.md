---
title: Creating and Customizing Tasks
sequence: 200
keywords: proget,packages
---

The built-in tasks are a good starting point, but as you expand Otter within your organization, you may want to customize tasks to help model governance and compliance policies.

A task has a name and description, and is comprised of a set of more granular security attributes that define what principals may or may not do when granted the task. Some attributes may be scoped to an Environment, whereas others can only be applied to the system scope. For example, orchestration jobs are managed at the system level (and can target any environment), so the "Manage Jobs" attribute cannot be scoped to a single environment.

## Proxy Security Attributes {#proxy data-title="Proxy Attributes"}

Any system that allows for granular security attributes has the concept of "granting by proxy", or proxy attributes. For example, even if you only grant permission to create and delete files on a file system, you are still granting edit permission by proxy; a user can simply delete and then create a file as a workaround for not being able to edit it. Likewise, users with only edit permissions can delete by proxy, because they can simply edit the file to have no content.

There is no sensible way for any software to prevent proxy operations, which is why it's important to understand what proxy attributes you may be unintentionally allowing.

## Invalid Security Attributes {#invalid data-title="Invalid Attributes"}

Granular security attributes can also lead towards creating "nonsensical" policies. For example, giving manage but not view permissions is nonsensical because you can't manage something you can't view. In this case, the behavior is undefined; generally, you'll be able to view the thing, but there are some places where it might not be visible.

While you wouldn't intentionally create such a policy, it might occur if you create multiple overlapping restrictions and permissions across multiple overlapping groups. The best way to mitigate this risk is to carefully plan your use of restrictions.

## Transitive Access {#transitive data-title="Transitive Access"}

Even with well-planned security restrictions in one part of a system, users can bypass those restrictions from another part of the system that didn't consider the same security restrictions. For example, simply denying view privileges on sensitive files on a network drive is not enough to protect sensitive data; anyone with access to the backups will have transitive access to all those files.  Of course, you can encrypt the backups, but then anyone with the decryption key will have transitive access to all the backups.

With Otter, it's especially important to consider transitive access. If Otter can access a production server, and you give users administrative access to Otter, then they can also deploy to that server. There's just no way around that.

## Security Attribute Reference {#reference data-title="Reference"}

Some of the attributes are denoted with a superscript E, which indicates that they are scopable by environment.

![all permissions](/resources/documentation/otter/2/tasks-permissions.png){.screenshot}

### Admin (Configure Otter)

It is a sort of "administrative catch all" that is used to secure pages in the administration section not covered by other attributes, including security, extensions, and all settings.

:::attention {.best-practice}
This attribute is a proxy for all other attributes, and should be only granted to people you intend to be system administrators.
:::

### Environments (Manage)

This allows users to create, edit, and delete environments.

:::attention {.best-practice}
This attribute may provide transitive access to all environment-scoped restrictions. For example, a user restricted from performing certain tasks in **Production** can simply delete that environment.
:::

### Roles (Manage)

This allows users to create, edit, and delete roles, as well as assign roles to servers, which in turn may cause those servers to be targeted in jobs or configured in a particular manner.

### Assets (Manage^E^, View^E^)

View allows users to open assets (scripts, modules, templates) and view the contents of assets stored within a raft associated with a specific environment.

:::attention {.best-practice}
You shouldn't put sensitive information (like passwords) in your assets, so there's no good reason to restrict view.
:::

Manage will allow users to create, edit, and delete assets.

:::attention {.best-practice}
Because plans can already run arbitrary commands, and editing assets used by plans can enable any behavior, there's little security value in allowing managing assets, but not plans. However, you may wish to limit by responsibility, and may find this a convenient way to do it.
:::

### Executions (View^E^, View Debug Logs^E^)

This allows users to view the details of executions, such as configuration or orchestration jobs. These are primarily designed to restrict sensitive data that is inconsequently logged. The "Debug" logs will hide only the debug-level logs (which is where most tools will log sensitive data).

### Servers (View^E^, Manage)

View allows users to view a server’s details and configuration, although viewing the variables and configuration plans require separate attributes. There are very rare circumstances where you wouldn’t grant viewing of all.

:::attention {.technical}
This can provide transitive access to all connected servers and should be granted with extreme care.
:::

You can grant managing variables on servers without granting management of servers themselves.

### Configuration Plans (View^E^, Manage^E^)

View allows users to open configuration plans on servers, and manage allows them to edit and delete them.

### Jobs (View History^E^, Manage^E^)

View allows users to see which jobs have been run, and the details of that job. Note that it does not allow viewing of execution logs.

Manage allows creating, editing, and deleting jobs. Note that this allows users to run orchestration jobs against arbitrary servers, and should be granted with caution.

### Variables (View^E^, Manage^E^)

View allows users to see the values (unless obscured) of variables, whereas manage allows users to create, edit, and delete, as well as perform bulk edits. Note that creating or editing multi-scope variables requires system-level permissions.

### Credentials (Manage^E^ and View Passwords^E^)

View Passwords will allow users to view sensitive (encrypted) fields on resource credentials.

:::attention {.best-practice}
This attribute primarily exists to allow some users to view credentials only associated with certain environments.
:::

Manage will allow users to create, edit, and delete credentials, as well as view all sensitive (encrypted) fields.

:::attention {.technical}
This could provide transitive access to connected systems, so be very careful when granting this.
:::

### Orchestration Plans (View^E^, Manage^E^)

View allows users to open orchestration plans.

Manage allows creating, editing, and deleting of Orchestration Plans.
