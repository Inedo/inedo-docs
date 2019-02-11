---
title: Creating and Customizing Tasks
sequence: 200
keywords: proget,packages
---

  The built-in tasks are a good starting point, but as you expand ProGet within your organization, you may want to customizing tasks to help model governance and compliance policies.     

  A task has a name and description, and is comprised of a set of more granular security attributes that define what principals may or may not do when granted the task. Some attributes may be scoped to a feed, whereas others can only be applied to the system scope. For example, scheduled jobs are managed at the system level (and are used by all feeds), the "Scheduled Jobs" attribute cannot be scoped to a single feed.

  ## Proxy Security Attributes {#proxy data-title="Proxy Security Attributes"}

  Any system that allows for granular security attributes has the concept of "granting by proxy", or proxy attributes. For example, even if you only grant permission to create and delete files on a file system, you are still granting edit permission by proxy; a user can simply delete and then create a file as a workaround for not being able to edit it. Likewise, users with only edit permissions can delete by proxy, because they can simply edit the file to have no content.

  There is no sensible way for any software to prevent proxy operations, which is why it's important to understand what proxy attributes you may be unintentionally allowing.

  ## Nonsensical Security Attributes {#nonsensical data-title="Nonsensical Security Attributes"}

  Granular security attributes can also lead towards creating "nonsensical" policies. For example, giving manage but not view permissions is nonsensical because you can't manage something you can't view. In this case, the behavior is undefined; generally, you'll be able to view the thing, but there are some places where it might not be visible.  

  While you wouldn't intentionally create such a policy, it might occur if you create multiple overlapping restrictions and permissions across multiple overlapping groups. The best way to mitigate this risk is to carefully plan your use of restrictions.

## Transitive Access {#transitive data-title="Transitive Access"}

Even with a well-planned security restrictions in one part of a system, users can bypass those restrictions from another part of the system that didn't consider the same security restrictions. For example, simply denying view privileges on sensitive files on a network drive is not enough to protect sensitive data; anyone with access to the backups will have transitive access to all of those files.  Of course, you can encrypt the backups, but then anyone with the decryption key will have transitive access to all the backups.

## Security Attribute Reference {#security data-title="Security Attribute Reference"}

Some of the attributes are denoted with a superscript F, which indicates that they are scopable by feed.

### Admin (Configure ProGet) {#admin data-title="Admin (Configure ProGet)"}

**This attribute is a proxy for all other attributes, and should be only granted to people you intend to be system administrators.** It is a sort of "administrative catch all" that is used to secure pages in the administration section not covered by other attributes, including security, extensions, and all settings.

### Connectors (Manage) {#connectors data-title="Connectors (Manage)"}

This allows users to create, edit, and delete connectors. This can provide transitive access to editing feeds that use a connector.

### Scheduled Jobs (View and Manage) {#scheduled data-title="Scheduled Jobs (View and Manage)"}

View will allow users to see the result of scheduled jobs. Manage will allow users to manually execute, and also deactivate and change the status of these jobs.

### Credentials (Manage and View Passwords) {#credentials data-title="Credentials (Manage and View Passwords)"}

View Passwords will allow users to view sensitive (encrypted) fields on resource credentials. This attribute primarily exists to allow some users to view credentials only associated with certain environments.

:::attention {.analogy}
Currently, ProGet only uses Credentials for advanced Active Directory configuration; eventually we will use credentials for connecting username/passwords as well.
:::

Manage will allow users to create, edit, delete credentials, as well as view all sensitive (encrypted) fields. This could provide transitive access to connected systems, so be very careful when granting this.

### Vulnerabilities (Manage) {#vulnerabilities data-title="Vulnerabilities (Manage)"}

Allows users to create, edit, and delete assessment types, as well as asses vulnerabilities.

### Licenses (Manage) {#licenses data-title="Licenses (Manage)"}

Allows users to create, edit, and delete known license types, as well as define licensing rules on feeds and globally. This is not feed-specific by design; many packages do not use standard SPDX codes, but instead specify a custom URL (often, to their project site or source repository), and it would be practically unusable to not be permitted to define new known license types, or to make known license types a feed-specific setting.

### Feeds (View Feed<sup>F</sup> and Download Package<sup>F</sup>) {#feeds data-title="Feeds (View Feed and Download Package)"}

These attributes are essentially required for read-only access to a feed. There are some very limited use cases for allowing viewing feed and package metadata, but not allowing packages themselves to be downloaded (or vice versa).

### Feeds (Packages: Add<sup>F</sup> , Pull<sup>F</sup>, Overwrite<sup>F</sup>, and Delete<sup>F</sup>) {#feeds data-title="Feeds (Packages: Add, Pull, Overwrite, and Delete)"}

These attributes all allow for manipulation of packages in a feed.

The Add attribute allows for UI- and API-based adding of packages, while the Pull attribute only allows for using the UI's "pull package" function. Granting only Pull may be useful for building a sort of approval workflow for connector packages.

Generally, you offer Add and Pull to package authors, but restrict Overwrite and Delete access to administrative users; packages are intended to be immutable (and can be automatically purged through retention policies), and giving this access encourages a suboptimal workflow.

Note: Delete with Add privileges are proxy attributes for Overwrite.

### Feeds (Accept Package Promotions<sup>F</sup>) {#feeds data-title="Feeds (Accept Package Promotions)"}

This attribute permits promoting a package from another feed (the "source" feed) into the feed this permission applies to. You should also grant View and Download Package permissions on the source feed.

### Feeds (Manage<sup>F</sup>) {#feeds data-title="Feeds (Manage)"}

This allows for full control over the feed, including changing package stores. It is effectively administrative access for the feed, and is a proxy attribute for all other attributes.
